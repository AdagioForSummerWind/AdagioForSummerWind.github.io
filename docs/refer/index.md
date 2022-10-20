# 


我们用图神经网络去做时空数据插补与BRITS相比，BRITS的弱势在哪里呢，我还不太明白……

26页倒数第二行 降 去掉


## 1 最近邻插补算法：关键评估。
https://bmcmedinformdecismak.biomedcentral.com/articles/10.1186/s12911-016-0318-z
[5]Lorenzo Beretta and Alessandro Santaniello. Nearest neighbor imputation algorithms: a critical evaluation. BMC medical informatics and decision making, 16(3):197–208, 2016.

出身背景
最近邻（NN）插补算法是填充缺失数据的有效方法，其中一些记录上的每个缺失值都被从整个记录集中的相关案例中获得的值替换。除了能够用尽可能接近真实值的合理值替换缺失数据外，插补算法还应保持原始数据结构，避免扭曲插补变量的分布。尽管NN算法效率很高，但人们对这些方法对数据结构的影响知之甚少。
方法
对具有不同模式和丢失程度的合成数据集进行仿真，以评估在不同学习框架下，单邻（1NN）和k邻无（kNN）或加权（wkN）的NN的性能：普通集、ReliefF滤波后的约简集、打包、属性的随机选择、，打包与属性的随机选择相结合（类似于随机森林的方法）。
后果
无论采用何种框架，就插补精度和减少推断统计误差而言，kNN通常优于1NN，但1NN是唯一能够保留数据结构的方法，即使考虑k个邻域的小值，数据也会失真；重采样模式的失真更严重。
结论
结合ReliefF使用三个邻居似乎可以在插补错误和数据结构保存之间取得最佳平衡。在完全随机引入缺失数据后，对单质子发射计算机断层扫描（SPECTF）心脏数据集进行插补实验时，可以得出相同的结论。


## 2 缺失数据：神经网络和期望最大化技术的比较

https://www.jstor.org/stable/24099079#metadata_info_tab_contents
[39]Fulufhelo V Nelwamondo, Shakir Mohamed, and Tshilidzi Marwala. Missing data: A comparison of neural network and expectation maximization techniques. Current Science, pp. 1514–1521, 2007.

最近的文献中出现了两种技术作为缺失数据插补问题的候选解决方案。 这些是期望最大化（EM）算法和自联想神经网络和遗传算法（GA）的组合。 这两种技术都已单独讨论过，并且在现有文献中详细讨论了它们的优点。 但是，它们尚未相互比较。 本文使用工业发电厂、工业缠绕过程和 HIV 血清流行率调查数据的数据集对这两种技术进行了比较。 结果表明，EM算法更适用于输入变量之间几乎没有或没有相互依赖的情况，而自联想神经网络和GA组合适用于某些给定变量之间存在固有非线性关系的情况。 .

## 3 通过状态空间方法进行时间序列分析
https://books.google.com.hk/books?hl=zh-CN&lr=&id=lGyshsfkLrIC&oi=fnd&pg=PP1&dq=Time+series+analysis+by+state+space+methods&ots=WP4NWftwhp&sig=is-tbib2NZ7zzrtpTfvxy3VaGxI&redir_esc=y#v=onepage&q=Time%20series%20analysis%20by%20state%20space%20methods&f=false
[16]James Durbin and Siem Jan Koopman. Time series analysis by state space methods. Oxford university press, 2012.



## 4 用于大规模非负矩阵和张量分解的快速局部算法
https://d.wanfangdata.com.cn/periodical/91669bdffdc7025f9faab9d298aeaa39

[13]Andrzej Cichocki and Anh-Huy Phan. Fast local algorithms for large scale nonnegative matrix and tensor factorizations. IEICE transactions on fundamentals of electronics, communications and computer sciences, 92(3):708–721, 2009.

非负矩阵分解 (NMF) 及其扩展如非负张量分解 (NTF) 已成为盲源分离 (BSS)、图像数据库分析、数据挖掘和其他信息检索和聚类应用的重要技术。在本文中，我们提出了一系列有效的 NMF/NTF 算法，以及稀疏非负编码和表示，在计算神经科学、多感官处理、压缩感知和多维数据分析中具有许多潜在的应用。我们开发了一类优化的局部算法，称为分层交替最小二乘 (HALS) 算法。出于这些目的，我们对一组平方欧几里得距离执行了顺序约束最小化。然后，我们将这种方法扩展到使用 alpha 和 beta 散度的稳健成本函数，并得出灵活的更新规则。我们的算法是局部稳定的，并且适用于基于 NMF 的盲源分离 (BSS)，不仅适用于超定情况，而且适用于欠定（过完备）情况（即，对于传感器数量少于来源）如果数据足够稀疏。 NMF 学习规则针对 N 阶非负张量分解 (NTF) 进行了扩展和推广。此外，这些算法可以通过调整单个参数来调整到不同的噪声统计数据。广泛的实验结果证实了所开发算法的准确性和计算性能，特别是在使用多层分层 NMF 方法时。


辅导员-袁梦娇老师 18850312623  
2023届毕业生求职创业补贴申请】@全体成员   
各位同学，关于2023届毕业生求职创业补贴申请工作通知如下：
http://job.hitsz.edu.cn/xwtzxq?lx=0&id=97fbe8c490ab4e8f89598c9c56090d01
一、申请条件和补贴标准
有就业创业意愿并积极求职或开展创业准备，且符合以下情形之一的学生，可申请一次性3000元的求职创业补贴（研究生学历毕业生如在本科毕业阶段已领取求职创业补贴的，符合多项条件的，都不得重复申请）：
1.城乡困难家庭毕业生；
2.残疾毕业生；
3.在学期间已获得国家助学贷款的毕业生。
二、申请办法
1、学生注册。“广东政务服务网深圳市”实名注册账号(网址：http://www.gdzwfw.gov.cn)
2、提出申请。登录业务系统（网址：https://hrsspub.sz.gov.cn/ggjyfw/）→个人用户→注册→提出补贴预申请并上传相关材料（详见通知）。
3、学院预审。学生系统预申请成功后，于2021年10月8日17:00之前，向辅导员提交申请材料（具体材料和要求见通知）。未在规定期限内提出申请的或提出申请未递交书面材料的，视为自动放弃申请资格。
4、审核和发放。学校审核通过的，市公共就业服务机构复核，复核无误后学校完成7天公示，公示后5个工作日放款。

注意：
1、所有资料审核必须要验原件，学生上传资料要清晰、完整、有效；
2、所有证件有学生名字的，给予通过，没有需要提供关系证明，仅限父母；（证件封面一定要上传）；
3、若存在国家助学贷款无电子合同，需学院核实信息并在申请名单中备注，将由招生就业处统一出具名单（加盖学校就业处章）及银行出具的证明；
4、学生申报补贴时确认所填报银行卡是否正常使用，避免后续无法支付。

系统申请常见问题说明：（1） 家庭地址打印后手动填写即可；（2） 系统内若不能修改银行卡号可能是学生名下的社保卡或其他银行卡；（3）学校请选“哈尔滨工业大学（深圳）”

## 5 


[9]Wei Cao, Dong Wang, Jian Li, Hao Zhou, Yitan Li, and Lei Li. Brits: bidirectional recurrent imputation for time series. In Proceedings of the 32nd International Conference on Neural Information Processing Systems, pp. 6776–6786, 2018.


## 6 具有缺失值的多元时间序列的递归神经网络


[10]Zhengping Che, Sanjay Purushotham, Kyunghyun Cho, David Sontag, and Yan Liu. Recurrent neural networks for multivariate time series with missing values. Scientific reports, 8(1):1–12,2018
实际应用中的多变量时间序列数据，如医疗保健、地球科学和生物学，具有多种缺失值的特点。在时间序列预测和其他相关任务中，已经注意到缺失值及其缺失模式通常与目标标签相关，即信息缺失。在利用缺失模式进行有效插补和提高预测性能方面的工作非常有限。在本文中，我们开发了新颖的深度学习模型，即 GRU-D，作为早期尝试之一。 GRU-D 基于门控循环单元 (GRU)，这是一种最先进的循环神经网络。它采用两种缺失模式的表示，即掩码和时间间隔，并将它们有效地结合到一个深度模型架构中，这样它不仅可以捕获时间序列中的长期时间依赖性，还可以利用缺失的模式来实现更好的预测结果。在真实世界临床数据集（MIMIC-III、PhysioNet）和合成数据集上的时间序列分类任务实验表明，我们的模型实现了最先进的性能，并为更好地理解和利用时间序列中的缺失值提供了有用的见解分析。









## 青基
（基于插值和因子分解的补全模型）早期的工作包括基于平滑曲线假设的插值方法[35] [36]，这些方法假设整个时间序列数据是平滑的，因此主要利用局部观测进行重构丢失数据，因而忽略了数据在时间上的动态性。文献[37]首先随机初始化丢失值，然后基于链式方程对丢失值进行估计。文献[38]将丢失值视为隐含变量，通过学习其转移矩阵对隐含变量进行推断。文献[39]将多变量时间序列数据视为矩阵，并通过矩阵分解得到时间不变量因子和时间动态因子，并在时间动态因子上施加连续性约束，进而恢复丢失值。（基于深度学习的补全模型） 随着深度学习的广泛引用，文献[40][41][42]试图通过引入循环神经网络来进行时空数据恢复，文献[43]则试图通过对抗生成网络实现对含丢失值时间序列数据的建模，进而实现丢失值重构，文献[44]试图使用图卷积神经网络在道路网络上对交通流进行补全，并通过图卷积操作建模相邻路段的空间相关性。

（对现有补全模型的总结）时间序列补全模型沿着从局部插值到对时间动态建模的方向发展，尽管在一系列数据集上取得了不错的效果，但现有补全方法没有显示的建模（explicitly model）不同空间对象之间隐含的相关性，而这一空间相关性对于丢失值恢复至关重要，文献[44]依赖于道路网络定义的图拓扑结捕获空间相关性，然而不适用于图结构未知的场景；与此同时现有方法通常只能给出对丢失值的估计而无法量化这一估计值的置信度，而这种置信度对于使用估计值进行决策制定是不可或缺的。因此本项目拟通过贝叶斯深度生成模型去动态学习隐含的图结构，并整合隐含图结构和非线性动态来实现对丢失值的补全，由于贝叶斯生成模型可以输出对估计值的概率分布，因此新的模型不但能够进行丢失值估计同时可以提供其估计值的可信度。。

现有时空图上的补全模型多数是基于矩阵或者张量分解的因子模型，这类
因子模型用隐含因子来表示每个空间节点，同时对每个时间切片赋予一个隐含
因子，然后基于矩阵或张量分解的技术并结合时间上的动态性，为每个隐含因
子学习表征，使得所学表征在观测数据上误差最小。也有部分模型通过修改循
环神经网络或通过生成对抗网络来建模时间序列数据的。这些模型都没有显示
的去推断隐含的图结构，本项目则从推断隐含图结构出发并结合图上的学习算
法来创新性的解决这一问题。

研究思路：
图 4 展示了从含有丢失值的时空数据中推断隐含图结构并使用推断的图结
构进行数据补全的整体研究思路。其整体研究思路和未知结构时空图上的预测
问题类似，但不同的地方在于如何建模含有丢失值的时空数据。首先，如何从含有丢失值的时间序列数据��中提取表征并将其用于图结构推断。循环神经网络
无法直接处理含丢失值的序列数据，因为其每一步都需要观测数据更新隐含表
征ℎ�，下面通过分析 GRU 循环神经网络的更新机制来说明这一问题，以及提出
可行的修改方案，为了叙述清晰，暂时忽略节点的下标，即用��来代替��,�。


GRU 循环神经网络通过隐含表征ℎ�来表示从开始到当前时刻的序列，并通过每
一步观测��对其动态更新，其中有两个门��
, ��来控制信息流的大小，而��
, ��以及
新引入的观测信息ℎ�
' 由当前观测��和历史表征ℎ�−1共同决定。对于有丢失值的时
间序列来说当前观测��可能是丢失的，因此无法控制相应的信息流。本项目拟采
用如下方案对 GRU 做出修改，将修改后的模型称为 D-GRU（Decayed-GRU）。
令��为与��有相同维度的 0/1 向量，如果当前时间步有观测其值为 1，否则为 0，
Δ�表示当前时间步距离上一步有观测值时间步的间隔，















[35]Kreindler, David M., and Charles J. Lumsden. The Ef ects of the Irregular Sample and Missing Data in Time Series Analysis. Nonlinear dynamics, psychology, and life sciences (2006).

[36]Fung, David S. Methods for the estimation of missing values in time series. (2006).

[37]Azur, Melissa J., Elizabeth A. Stuart, Constantine Frangakis, and Philip J. Leaf. Multiple imputation by chained equations: what is it and how does it work?. International journal of methods in psychiatric research 20, no. 1 (2011): 40-49.

[38]Li, Lei, James McCann, Nancy S. Pollard, and Christos Faloutsos. Dynammo: Mining and summarization of coevolving sequences with missing values. In Proceedings of the 15th ACM SIGKDD international conference on Knowledge discovery and data mining, pp. 507-516. 2009.

[39]Yu, Hsiang-Fu, Nikhil Rao, and Inderjit S. Dhillon. Temporal regularized matrix factorization for high-dimensional time series prediction. Advances in neural information processing systems 29 (2016).

[40]Che, Zhengping, Sanjay Purushotham, Kyunghyun Cho, David Sontag, and Yan Liu. Recurrent neural networks for multivariate time series with missing values. Scientific reports 8, no. 1 (2018): 1-12.

[41]Yoon, Jinsung, William R. Zame, and Mihaela van der Schaar. Multi-directional recurrent neural networks: A novel method for estimating missing data. In Time Series Workshop in International Conference on Machine Learning. 2017

[42]Cao, Wei, Dong Wang, Jian Li, Hao Zhou, Lei Li, and Yitan Li. Brits: Bidirectional recurrent imputation for time series. Advances in neural information processing systems 31 (2018).

[43]Luo, Yonghong, Xiangrui Cai, Ying Zhang, and Jun Xu. Multivariate time series imputation with generative adversarial networks. Advances in neural information processing systems 31 (2018).

[44]Hu, Jilin, Chenjuan Guo, Bin Yang, and Christian S. Jensen. Stochastic weight completion for road networks using graph convolutional networks. IEEE 35th International Conference on Data Engineering (ICDE), pp. 1274-1285. 2019.








1. 就业推荐表，不轻易给原件，就业处盖章不处理急章
2. 成绩单 T3 T4自助打印
3. 签三方 ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20221012111327.png)
   点击发起，企业确认就已经代表签约了。
4. ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20221012111756.png)
   重要，确认企业档案保管权，不确定就填回生源地
5. 试用期不超过6个月
6. 任何附加内容落于纸质
7. 解约![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20221012112603.png)

/*
 * @Author: jf-011101 2838264218@qq.com
 * @Date: 2022-08-02 14:03:25
 * @LastEditors: jf-011101 2838264218@qq.com
 * @LastEditTime: 2022-08-21 11:07:06
 * @FilePath: \dytt\dal\db\user.go
 * @Description: User database operation business logic
 */
/*
 * @Author: jf-011101 2838264218@qq.com
 * @Date: 2022-08-02 14:03:24
 * @LastEditors: jf-011101 2838264218@qq.com
 * @LastEditTime: 2022-08-21 11:00:28
 * @FilePath: \dytt\cmd\api\handlers\user.go
 * @Description: define User API's handler, pass context to rpc client and get response
 */

package handlers

import (
	"context"
	"fmt"
	"net/http"
	"strconv"

	"github.com/gin-gonic/gin"

	"github.com/jf-011101/dytt/dal/db"
	"github.com/jf-011101/dytt/dal/pack"
	"github.com/jf-011101/dytt/grpc_gen/user"
	"github.com/jf-011101/dytt/internal/api/rpc"
	"github.com/jf-011101/dytt/pkg/errno"
)

var hint db.RpcMsg

func Register(c *gin.Context) {
	var registerVar UserRegisterParam
	registerVar.UserName = c.PostForm("username")
	registerVar.PassWord = c.PostForm("password")

	if len(registerVar.UserName) == 0 || len(registerVar.PassWord) == 0 {
		SendResponse(c, pack.BuilduserRegisterResp(errno.ErrBind))
		return
	}

	resp, err := rpc.Register(context.Background(), &user.DouyinUserRegisterRequest{
		Username: registerVar.UserName,
		Password: registerVar.PassWord,
	})
	if err != nil {
		SendResponse(c, pack.BuilduserRegisterResp(errno.ConvertErr(err)))
		return
	}
	SendResponse(c, resp)
}

func Login(c *gin.Context) {
	var registerVar UserRegisterParam
	registerVar.UserName = c.PostForm("username")
	registerVar.PassWord = c.PostForm("password")

	if len(registerVar.UserName) == 0 || len(registerVar.PassWord) == 0 {
		SendResponse(c, pack.BuilduserRegisterResp(errno.ErrBind))
		return
	}

	resp, err := rpc.Login(context.Background(), &user.DouyinUserRegisterRequest{
		Username: registerVar.UserName,
		Password: registerVar.PassWord,
	})
	if err != nil {
		SendResponse(c, pack.BuilduserRegisterResp(errno.ConvertErr(err)))
		return
	}
	SendResponse(c, resp)
}

func Refresh(c *gin.Context) {
	fmt.Print("11111111111111111111111")
	resp, err := rpc.Refresh(context.Background(), &user.DouyinUserRefreshRequest{})
	r:=*resp
	respon,_ := r.Recv()
	fmt.Print("h!!!", *hint.Data)
	hint.Data.Cols = respon.Data.Cols
	fmt.Print("ed")
	hint.Data.Rows = respon.Data.Rows
	hint.Data = db.AssignHintData(hint.Data, respon.Data.Data)

	fmt.Print(hint.Data.Cols)
	fmt.Print("22222222222222222222222")
	error_type := "刷新失败"
	if err != nil {
		fmt.Print(err)
		error_type = ""

	}
	c.HTML(http.StatusOK, "pir.html", gin.H{
		"error_type": error_type,
	})
}

func QueryUserBoundary(c *gin.Context) {
	error_type := ""
	c.HTML(http.StatusOK, "pir.html", gin.H{
		"error_type": error_type,
	})
}

func QueryUser(c *gin.Context) {
	var query *db.RpcMsg
	pi := &db.SimplePIR{}
	N := uint64(10)
	d := uint64(8)
	p := pi.PickParams(N, d, db.SEC_PARAM, db.LOGQ)

	D := db.SetupDB(N, d, &p)

	shared_state := pi.Init(D.Info, p)

	var QueryVar UserQueryParam
	QueryVar.PhoneNumber = c.PostForm("phone-number")
	q, _ := strconv.Atoi(QueryVar.PhoneNumber)

	index_to_query := uint64(q)
	client_state, msg := pi.Query(index_to_query, shared_state, p, D.Info)
	fmt.Print(client_state)
	query = db.Msg2RpcMsg(&msg)
	queryData := Matrix2UserMatrix(query.Data)
	_, err := rpc.QueryUser(context.Background(), &user.DouyinUserQueryRequest{
		QueryData: queryData,
	})
	error_type := "存在"
	if err != nil {
		fmt.Print(err)
		error_type = "不存在"
	}

	c.HTML(http.StatusOK, "pir.html", gin.H{
		"error_type": error_type,
	})
}

func Matrix2UserMatrix(r *db.RpcMatrix) *user.Matrix {
	q := &user.Matrix{}

	q.Cols = r.Cols
	q.Rows = r.Rows

	copy(q.Data, r.Data)

	return q
}

// 传递 获取注册用户`UserID`操作 的上下文至 User 服务的 RPC 客户端, 并获取相应的响应.
func GetUserById(c *gin.Context) {
	var userVar UserParam
	userid, err := strconv.Atoi(c.Query("user_id"))
	if err != nil {
		SendResponse(c, pack.BuilduserUserResp(errno.ErrBind))
		return
	}
	userVar.UserId = int64(userid)
	userVar.Token = c.Query("token")

	if len(userVar.Token) == 0 || userVar.UserId < 0 {
		SendResponse(c, pack.BuilduserUserResp(errno.ErrBind))
		return
	}

	resp, err := rpc.GetUserById(context.Background(), &user.DouyinUserRequest{
		UserId: userVar.UserId,
		Token:  userVar.Token,
	})
	if err != nil {
		SendResponse(c, pack.BuilduserUserResp(errno.ConvertErr(err)))
		return
	}
	SendResponse(c, resp)
}


