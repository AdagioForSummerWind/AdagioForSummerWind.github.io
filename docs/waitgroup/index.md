# WaitGroup 实现原理

# WaitGroup 实现原理记录
源码：
```go
// go1.18 D:\Go1.18\src\sync\waitgroup.go

// A WaitGroup waits for a collection of goroutines to finish.
// The main goroutine calls Add to set the number of
// goroutines to wait for. Then each of the goroutines
// runs and calls Done when finished. At the same time,
// Wait can be used to block until all goroutines have finished.
//
// A WaitGroup must not be copied after first use.
type WaitGroup struct {
	noCopy noCopy

	// 64-bit value: high 32 bits are counter, low 32 bits are waiter count.
	// 64-bit atomic operations require 64-bit alignment, but 32-bit
	// compilers only guarantee that 64-bit fields are 32-bit aligned.
	// For this reason on 32 bit architectures we need to check in state()
	// if state1 is aligned or not, and dynamically "swap" the field order if
	// needed.
	state1 uint64
	state2 uint32
}


// state returns pointers to the state and sema fields stored within wg.state*.
func (wg *WaitGroup) state() (statep *uint64, semap *uint32) {
	if unsafe.Alignof(wg.state1) == 8 || uintptr(unsafe.Pointer(&wg.state1))%8 == 0 {
		// state1 is 64-bit aligned: nothing to do.
		return &wg.state1, &wg.state2
	} else {
		// state1 is 32-bit aligned but not 64-bit aligned: this means that
		// (&state1)+4 is 64-bit aligned.
        //将state1的地址显式转换为一个[3]uint32的地址，因为
        //state1和state2在内存中是连续的，所以将64位的state1拓展
        //至总长度为96位的数组时，默认将字段state2作为state[2]的
        //数据了，所以这里返回的statep是state1字段的低32位加上字
        //段state2的值，sema是state1的高32位。
		state := (*[3]uint32)(unsafe.Pointer(&wg.state1))
		return (*uint64)(unsafe.Pointer(&state[1])), &state[0]
	}
}

// Add adds delta, which may be negative, to the WaitGroup counter.
// If the counter becomes zero, all goroutines blocked on Wait are released.
// If the counter goes negative, Add panics.
//
// Note that calls with a positive delta that occur when the counter is zero
// must happen before a Wait. Calls with a negative delta, or calls with a
// positive delta that start when the counter is greater than zero, may happen
// at any time.
// Typically this means the calls to Add should execute before the statement
// creating the goroutine or other event to be waited for.
// If a WaitGroup is reused to wait for several independent sets of events,
// new Add calls must happen after all previous Wait calls have returned.
// See the WaitGroup example.
func (wg *WaitGroup) Add(delta int) {
	statep, semap := wg.state()
	if race.Enabled {
		_ = *statep // trigger nil deref early
		if delta < 0 {
			// Synchronize decrements with Wait.
			race.ReleaseMerge(unsafe.Pointer(wg))
		}
		race.Disable()
		defer race.Enable()
	}
	state := atomic.AddUint64(statep, uint64(delta)<<32)
	v := int32(state >> 32)
	w := uint32(state)
	if race.Enabled && delta > 0 && v == int32(delta) {
		// The first increment must be synchronized with Wait.
		// Need to model this as a read, because there can be
		// several concurrent wg.counter transitions from 0.
		race.Read(unsafe.Pointer(semap))
	}
	if v < 0 {
		panic("sync: negative WaitGroup counter")
	}
	if w != 0 && delta > 0 && v == int32(delta) {
		panic("sync: WaitGroup misuse: Add called concurrently with Wait")
	}
	if v > 0 || w == 0 {
		return
	}
	// This goroutine has set counter to 0 when waiters > 0.
	// Now there can't be concurrent mutations of state:
	// - Adds must not happen concurrently with Wait,
	// - Wait does not increment waiters if it sees counter == 0.
	// Still do a cheap sanity check to detect WaitGroup misuse.
	if *statep != state {
		panic("sync: WaitGroup misuse: Add called concurrently with Wait")
	}
	// Reset waiters count to 0.
	*statep = 0
	for ; w != 0; w-- {
		runtime_Semrelease(semap, false, 0)
	}
}

// Done decrements the WaitGroup counter by one.
func (wg *WaitGroup) Done() {
	wg.Add(-1)
}

// Wait blocks until the WaitGroup counter is zero.
func (wg *WaitGroup) Wait() {
	statep, semap := wg.state()
	if race.Enabled {
		_ = *statep // trigger nil deref early
		race.Disable()
	}
	for {
		state := atomic.LoadUint64(statep)
		v := int32(state >> 32)
		w := uint32(state)
		if v == 0 {
			// Counter is 0, no need to wait.
			if race.Enabled {
				race.Enable()
				race.Acquire(unsafe.Pointer(wg))
			}
			return
		}
		// Increment waiters count.
		if atomic.CompareAndSwapUint64(statep, state, state+1) {
			if race.Enabled && w == 0 {
				// Wait must be synchronized with the first Add.
				// Need to model this is as a write to race with the read in Add.
				// As a consequence, can do the write only for the first waiter,
				// otherwise concurrent Waits will race with each other.
				race.Write(unsafe.Pointer(semap))
			}
			runtime_Semacquire(semap)
			if *statep != 0 {
				panic("sync: WaitGroup is reused before previous Wait has returned")
			}
			if race.Enabled {
				race.Enable()
				race.Acquire(unsafe.Pointer(wg))
			}
			return
		}
	}
}
```

- WaitGroup 底层是一个结构体，逻辑上包含两个关键属性，`state`( `counter` 和 `waiter` 构成) 和 `sema` ，由`state1` `state2` 两个字段得到，考虑到内存对齐以及并发安全 对不同位数 OS 有着不同的设计
- `counter` 代表目前尚未调用的`WaitGroup.Done()` 的 `groutine` 的个数。`WaitGroup.Add(n)` 将会导致 `counter += n`, 而 `WaitGroup.Done()` 将导致 `counter--`。
- `waiter` 代表目前已调用 `WaitGroup.Wait()` 的 `goroutine` 的个数。
- `sema` 对应于 `golang` 中 `runtime` 内部的信号量的实现。`WaitGroup` 中会用到 `sema` 的两个相关函数，`runtime_Semacquire` 和 `runtime_Semrelease`。`runtime_Semacquire` 表示增加一个信号量，并挂起 当前 goroutine。`runtime_Semrelease` 表示减少一个信号量，并唤醒 sema 上其中一个正在等待的 goroutine。
- 对于64位对齐的情况，`state` 就是字段 `state1` 的值，`sema` 就是字段 `state2` 的值
- 对于32位对齐的情况，`state` 是字段 `state1` 的低32位加上`state2` ，`sema` 是 `state1` 的高32位。

1. 当调用 `WaitGroup.Add(n)` 时，`counter` 将会自增: `counter += n`
2. 当调用 `WaitGroup.Wait()` 时，会将 `waiter++`。同时调用 `runtime_Semacquire(semap)`, 增加信号量，并挂起当前 `goroutine`。 
3. 当调用 `WaitGroup.Done()` 时，将会 `counter--`。如果自减后的 `counter` 等于 0，会调用 `runtime_Semrelease` 释放信号量，唤醒因 `WaitGroup.Wait` 而挂起的 `goroutine`。




## 参考
https://juejin.cn/post/7120548108966035470

[go1.18](https://tip.golang.org/doc/go1.18)源码
