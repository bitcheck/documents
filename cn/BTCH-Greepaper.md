# BTCH - GREEPAPER（BTCH绿皮书）

### 本文档介绍`bitCheck`治理币`BTCH`

## 1 命名
`bitCheck`是一个基于区块链技术的去中心化金融（DeFi）应用，为交易提供担保支付。在传统贸易中，支票（Check）是一种非常常见的支付方式，与现金相比，支票作为一种信用工具，为交易双方提供了支付，资金融通等功能。

`bitCheck`将支票的功能在区块链上实现，取名为`bitCheck`（Check即为支票），并取其前四个辅音字母组成：`BTCH`。同时，`BTCH`也是比特币（BTC）和比特现金（BCH）合体。

## 2 BTCH的使命
`BTCH`是`bitCheck`的治理币，是`BTCH`社区运作的纽带，是社区成员身份的标志，是推动社区发展的重要动能，是`bitCheck`应用生态建设的助推器。

## 3 `BTCH`持有人的权利与义务
### `BTCH`持有人享有以下权利：

* 持币人通过投票对区块链上的智能合约中的重要规则做调整。
* 持币人通过投票，选举治理委员会，治理委员会行驶日常管理职能。
* 投票人通过投票，选举处理交易与支付争议的仲裁员50名。
* 持币人通过投票，对挖矿奖励方案进行调整。
* 持币人通过投票，对手续费，仲裁费等收入的分配方案进行调整。
* 获取`BTCH`资本增值收益的权利。
* 随时购买与销售`BTCH`的权利。

### `BTCH`持有人须旅行以下义务：
* 推广`bitCheck`应用，为`BTCH`赋能。

## 4 代币
### 主币：`BTCH`
预售：0

硬顶：30万枚ERC20（以太坊），30万枚TRC20（波场）

总量从零开始，一份支付，产一份`BTCH`，无交易，无增发。

### 仲裁委员代币：`ACT`
每个被选出的仲裁委员拥有一个`NFT`（不可转让）代币，总量50枚，每个仲裁员一枚，用于对争议支付进行投票。仲裁委员资格被取消后，`NFT`自动失效。新增仲裁委员，持有新的`NFT`。

仲裁委员代币名称：`ACT`（Arbitration Committee Member Token）

此`NFT`仅用于争议仲裁投票，不能买卖，不能转让。

### 治理委员代币：`GCT`
每个被选出的治理委员拥有一个`GCT`（不可转让）代币，总量7枚，每个治理委员一枚，用于日常投票决策。治理委员资格被取消后，`NFT`自动失效。新增的治理委员，持有新的`NFT`。

治理委员代币名称：`GCT`（Govern Committee Member Token）

此`NFT`仅用于日常治理事物的投票，不能买卖，不能转账。


## 5 挖矿奖励算法
以下参考自智能合约：`TokenManager.sol`
 * The bonus will calculated with 5 factors:
 * 挖矿奖励由以下五个部分组成：
 * 1- Base bonus factor. This is base bonus factor, here we set it as 0.05
   
   第一部分是基础系数，默认为`0.05`。
   对应合约代码如下：
   ```
    uint256 public baseFactor = 50; // 50 means 0.05
   ```

 * 2- Amount after exponent. This will reduce the weight of whale capital. Here we set it as 2/3. That means if somebody deposit 100,000, will just caculatd busnus according to 100,000 ** (2/3) = 2154.435
  
   第二部分为平滑指数，用于降低参与挖矿的大资金的权重，默认为2/3。如：某人支付`100000 USDT`，实际计算奖励时，按`100000`的`2/3`次方，即：2154.43。

   如果另一个人支付`1000 USDT`，如不计算指数，支付`100000 USDT`的人获得的奖励将是后者的`100倍`，但计算指数后，支付`1000 USDT`的人实际权重为`100`，两者相差从`100倍`缩小到`21.54倍`。此参数可有效降低大规模投机资金的大规模进入。

   对应合约代码如下：
   ```
    uint256[] public exponent = [2, 3];// means 2/3
   ```

 * 3- Hours factor between Deposit Withdraw. This is let the people store the money in contract longer. 
    
   第三部分为付款/取款时间差系数。
   
   If below 1 hour, there will be no bonus token.
   
   如果存取款时间在1小时以内，将无任何`BTCH`奖励。
  
   from 1-24 hours, the factor is 0.05; 24-48 hours, factor is 0.15, etc.
  
   如果存取款相差时间从1小时到24小时内，此因子为`0.05`；24-48小时，为`0.15`。
   
   对应的智能合约代码：
   ```
    uint256[] public intervalOfDepositWithdraw = [1, 24, 48, 96, 192, 384, 720]; // hours of inverval between deposit and withdraw
    uint256[] public intervalOfDepositWithdrawFactor = [5000, 15000, 16800, 20600, 28600, 45500, 81500]; // 5000 will be devided by 1e5, means 0.05
   ```
   依据以上代码，整理列表如下：
   |时间间隔|系数|
   |:-|-:|
   |<1小时|0|
   |1-24小时|0.05|
   |24-48小时|0.15|
   |48-96小时（2-4天）|0.168|
   |96-192小时（4-8天）|0.206|
   |192-384小时（8-16天）|0.286|
   |384小时-720小时（16-30天）|0.455|
   |>720小时（30天以上）|0.815|

   This factor will be modified by council according to the market.

   时间差系数由治理委员会根据市场情况进行调节。

 * 4- Stage factor: If total mint token is 300,000, we will devided it into several stages. Each stage has special bonus times. Ex. if stage factor is 5, means in this stage, miner will get 5 times than normal.
  
   第四部分为阶段系数，总量30万的`BTCH`，将分为三个阶段挖掘，每10万枚一阶段，系数越大，收益越高。第一阶段，此因子为`5`，意味着将获取5倍于正常水平的奖励。

   对应合约代码如下：
   ```
    uint256[] public stageFactors = [5000, 2500, 1250]; // Stage factor, 5000 means 5, 2500 means 2.5, etc.
    uint256 public eachStageAmount = 1e11; // Each stage amount, if 100000, this amount will be 1e11 (including decimals)
   ```

   整理列表如下：
   |阶段|支付总金额|系数|
   |:-|:-|-:|
   |1|100,000|5|
   |2|100,000|2.5|
   |3|100,000|1.25|
  
 * 5- Price elastical factor. We want the mint quantity for each deposit can be different under different market. If the price is higher than normal, the factor will become smaller automaticly, and if the price go down, the factor will become smaller also. It is a Gaussian distribution, and the average price (normal price) is fee of deposit and withdrawal.
   
   第五部分为价格弹性系数，即奖励数量会根据`BTCH`的价格变化而变化，即奖励数会随着`BTCH`的价格上涨与下跌，呈现一个正态分布的释放形态。

   In this version, we will keep price elastical factor as 1.

   默认的价格弹性系数为`1`。

 * So the bonus amount will be:
   
   ```
   Bonus amount = Amount after exponent * Base bonus factor * hours factor * stage factor * price elastical factor.
   ```

   综上所述，奖励计算公式如下：

   
   ```
   BTCH数量 = 支付金额 ** (平滑指数) * 基础系数 * 时间差系数 * 阶段系数 * 价格弹性系数
   
   ** 为指数符号
   ```

   根据以上公式，可以得出产矿量与以下因素相关：
   * 越早挖矿，产出越多
   * 付款与收款间隔时间越长，产出越多
   * 支付金额越多，产出越多（但是非线性关系）
   *  支付金额必须要大于`产矿最少支付额`，即`minMintAmount`，才能获得挖矿奖励。
    
   以上得到的`BTCH`奖励在扣除矿税后，由付款方和收款方按`付款人挖矿奖励占比`（即：参数`depositerShareRate`）分别获得。

## 6 规则治理与调整
作为一个基于`DAO`治理的担保支付系统，与运营相关的系数皆由治理委员会进行投票后，在智能合约中调整。

可调整的规则参数如下：

|名称|合约参数|说明|以太坊数值|波场数值|
|:--|:--|:--|:--|:--|
|基础系数|baseFactor|见上|0.05|0.04|
|时间差|intervalOfDepositWithdraw|见上|1, 24, 48, 96, 192, 384, 720小时|同以太坊|
|时间差系数|intervalOfDepositWithdrawFactor|见上|0.05, 0.15, 0.168, 0.206, 0.286, 0.455, 0.815|同以太坊|
|阶段系数|stageFactors|见上|5,2.5,1.25|同以太坊|
|阶段金额|eachStageAmount|见上|100000,100000,100000|同以太坊|
|平滑指数|exponent|见上|2/3|同以太坊|
|手续费率|feeRate|平滑指数后的手续费|16.67%|20%|
|优惠手续费额度|minChargeFeeAmount|为奖励小额支付，低于此额度享受优惠手续费（与产矿最少支付额配合使用，低于此额度的没有挖矿，但可享受优惠手续费）,如果超过，则先计算指数系数，然后按feeRate计算|500U|500U|
|优惠手续费最少费用|minChargeFee|优惠情况下最少收的费用|0|0|
|优惠手续费比例|minChargeFeeRate|优惠情况下的收费比例|0.1%|0.1%|
|产矿最少交易额|minMintAmount|低于此额度，将不产矿|500U|500U|
|矿税税率|taxRate||5%|5%|
|仲裁固定费用|councilJudgementFee|提交仲裁委员会仲裁的最低收费|0|0|
|仲裁比例费用|councilJudgementFeeRate|提交仲裁委员会仲裁的收费比例|17%|17%|
|付款人挖矿奖励占比|depositerShareRate|即扣除矿税后，付款人获得的奖励比例，余下的给收款人|50%|50%|
|矿税收取地址|taxBereauAddress|即矿税将打入该地址|||
|默认收取手续费地址|commonWithdrawAddress|对于未指定支付网关的支付，手续费打到这个账户|||
|治理委员会地址|councilAddress|该地址交给Aragon配置的投票委员会决策|||

### 7 BTCH基础价值
BTCH的基础价值来源于其收入模型，BTCH有以下几部分收入：
* 矿税
* 交易手续费
* 仲裁费
* 技术服务费

#### 7.1 矿税
举例：
某交易金额为`1000 USDT`，因高于产矿最少交易额`500 USDT`，可以有挖矿产出。

如果按`付款/取款时间差`为`6天`计算，`时间差系数`为`0.206`，早期矿工的`阶段奖励系数`为`5`，得出挖矿所得为：
```
1000 ** (2/3) * 0.05 * 0.206 * 5 = 5.15 BTCH。
```
矿税按`5%`计，为：`5.15BTCH * 5% = 0.2575 BTCH`

按付款方和收款方都得到`50%`奖励计，付款方和收款方各得到：`2.4625 BTCH`

#### 7.2 交易手续费
根据实际用户与挖矿用户的需求不同，将交易手续费费率分两类，一类是优惠手续费，一类是挖矿交易手续费。前者较低，后者较高。

* 优惠手续费 = 交易金额 * 优惠手续费比例 + 优惠手续费最少费用
* 挖矿交易手续费 = 交易金额 ** （平滑指数）* 手续费率
  
举例：
* 某交易金额`100 USDT`，因为不高于优惠手续费额度`500 USDT`，所以享受优惠交易手续费`0.1%`，即：`100 * 0.1% = 0.1 USDT`
  
* 某交易金额为`10000 USDT`，因为已高于优惠手续费额度，所以先计算平滑指数，再乘以手续费率`16.67%`。即：`10000 ** (2/3) * 16.67% = 16.67USDT`

#### 7.3 仲裁费
因为交易产生的纠纷，交易双方可以选择适合的仲裁委员会仲裁，并支付仲裁费用。

初始设置的仲裁费率为`17%`，由仲裁会员会成员平均分配，结算方式为`50%交易的币种+50%BTCH`。

其中`50% BTCH`由交易的币种在去中心化交易所购买。

#### 7.4 支付网关
对于接入`bitCheck`的第三方支付网关，收取`5000`个`BTCH`作为技术服务费用。

#### 7.5 币种定制
对于需要在DAPP中增加`ERC20（TRC20）`代币的用户，收取`3000`个`BTCH`作为技术服务费用。

