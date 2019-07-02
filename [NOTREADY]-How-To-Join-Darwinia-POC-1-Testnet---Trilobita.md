# How To Join Darwinia POC-1 Testnet - Trilobita



> darwinia钱包预计在POC-2之前推出，先用官方网页https://polkadot.js.org/apps

## 1. 使用操作界面

打开https://polkadot.js.org/apps/#/settings，x掉最上方的粉红色报警，看到如下界面：

![image-20190702104942567](https://hammerwang-1255810360.cos.ap-chengdu.myqcloud.com/2019-07-02-65721.jpg)

点击**Developer**标签，输入以下内容：

```json
{
  "TokenBalance": "u128",
  "Currency": "u128",
  "CurrencyOf": "u128",
  "RewardBalance": "u128",
  "RewardBalanceOf": "u128",
  "IndividualDeposit": {
    "month": "Moment",
    "start_at": "Moment",
    "value": "CurrencyOf",
    "claimed": "bool"
  },
  "Deposit": {
    "total": "CurrencyOf",
    "deposit_list": "Vec<IndividualDeposit>"
  }
}
```

再回到**General**界面，打开右上角的**custom endpoint**开关，然后在**remote node/endpoint to connect to** 填入：

```html
wss://trilobita.darwinia.network/
```

输入完成后点击下方最右侧蓝色button **Save&Reload**。这时页面就会自动刷新到可用界面。选择左侧的**Explorer**可看到如下：

![image-20190702130305863](https://hammerwang-1255810360.cos.ap-chengdu.myqcloud.com/2019-07-02-065736.jpg)

左下方黄色方框内会显示达尔文测试网的相关信息。



## 2. 生成/导入账户

### 2-1 生成新的账户

点击左侧toolbar中的**Accounts**可以看到如下界面，点击右上角**Add account**：

![image-20190702152807024](https://hammerwang-1255810360.cos.ap-chengdu.myqcloud.com/2019-07-02-072844.jpg)

在如下界面中填写新账户相关信息：

![image-20190702130816080](https://hammerwang-1255810360.cos.ap-chengdu.myqcloud.com/2019-07-02-065733.jpg)

- name: 账户名，起一个自己能记住的就好。
- mnemonic seed: 助记词/私钥。建议用笔抄下。
- password: 使用该账户操作时需要输入密码。
- Advanced creation options: 默认sr25519，无需改动。

生成之后点击**Save**， 之后会弹出一个对话询问你是否要保存账户信息到本地，可以继续点击**Create and backup account**.

![image-20190702131147003](https://hammerwang-1255810360.cos.ap-chengdu.myqcloud.com/2019-07-02-065725.jpg)

这时就可以在**Accounts**界面看到我们最新生成的账户地址。

为了接下来的staking操作，同理再生成另一个账户，这里取名tribolita_stash。



## 3. STAKING操作

> RING为Darwinia Network的基础token，KTON为staking token.
>
> tribolita_stash: 用于管理资金
>
> tribolita：用来操作

关于ring的转账，在**Transfer**页面完成。

### 3-1 获得KTON，用于Staking

获得KTON的唯一途径，就是存RING，在extrinsics页面中，选择操作账户（这里是tribolita_stash），选择kton -> deposit，依次填入需要存入的RING的数量和存入的月数（1-36)， 之后点击**Submit Transaction**：

![image-20190702132317393](https://hammerwang-1255810360.cos.ap-chengdu.myqcloud.com/2019-07-02-065724.jpg)

右上角会出现该次操作的事件，不过因为波卡官方和substrate之间存在版本不匹配的问题，所以事件确认目前无法使用，需要手动确认状态。等达尔文钱包上线之后，操作会方便很多。

这时，我们需要去手动确认链上状态，在deposit之后，系统会锁定用户的这部分RING，并且增发出KTON给用户。我们在**Chain state**界面中选择 **ktonBalances -> freeBalance**，选择需要查看的账户，点击左右侧的蓝色+号，即会出现该账户的KTON余额：

![image-20190702132728772](https://hammerwang-1255810360.cos.ap-chengdu.myqcloud.com/2019-07-02-065732.jpg)



### 3-2 STAKING

**Extrinsics -> staking -> bond**, 依旧使用tribolita_stash账户操作，

- controller：填入我们之前生成的tribolita账户（如果嫌麻烦的同学，也可以填入和操作账户一样的地址）；
- value：想用于staking的KTON的数额
- payee：目前只支持Stash (奖励金额打到操作账户)和Controller（奖励金额打到controller填入的账户）

![image-20190702133521218](https://hammerwang-1255810360.cos.ap-chengdu.myqcloud.com/2019-07-02-065728.jpg)



点击确认之后，继续去**Chainstate**查看状态。**Chain state -> staking -> bonded**，填入上一步的操作账户（tribolita_stash），如果成功的话就会出现刚才填入的controller账户，失败的话就会显示<empty>.

![image-20190702133943126](https://hammerwang-1255810360.cos.ap-chengdu.myqcloud.com/2019-07-02-65726.jpg)

 

### 3-3 提名

**extrinsics -> staking -> nominate**，填入你想投票的节点地址。



## 4. 成为验证人

### 4-1 设置SessionKey

#### 4-1-1 生成sessionkey

重复之前的账户生成， 在**Accounts** 生成sessionkey。需要注意的是，在点击**Add account**之后，在接下来跳出的界面中选择`ed25519`(之前是`sr25519`)，seed一栏建议选用 *Raw seed*（建议把seed保存下来）：

 ![image-20190702135152535](https://hammerwang-1255810360.cos.ap-chengdu.myqcloud.com/2019-07-02-065726.jpg)



### 4-2 绑定sessionkey

在**extrinsics -> session -> setKeys**界面，输入如下参数（注意！：这里的操作人是tribolita，即之前bond时填入的controller）：

- grandpaKey: 填入刚生成的sessionkey地址
- aurakey: 填入刚生成的sessionkey地址
- proof: 不填

![image-20190702141225014](https://hammerwang-1255810360.cos.ap-chengdu.myqcloud.com/2019-07-02-065727.jpg)

这时去**Chain state -> session -> nextKeyFor**查看，参数输入tribolita, 如果结果不是<empty>就说明绑定成功：

![image-20190702142009719](https://hammerwang-1255810360.cos.ap-chengdu.myqcloud.com/2019-07-02-65735.jpg)



### 4-3 启动节点

下载可执行二进制文件，在该文件同级目录下执行如下命令：

```bash
./darwinia \
--base-path /tmp/xxx\
--key 0x65cb3095a5da6be85e21a4a6a617e2f18fd0518a4e167614bc0c9bb9b3af2647 \
--validator \
--bootnodes /ip4/<OTHER IP Address>/tcp/<OTHER Port>/p2p/<OTHER Node ID> \
--port 20222 \
--name AliceDarwiniaNode \
--rpc-external \
--ws-external 
```

参数说明：

```bash
base-path: 保存链数据的地址
key: sessionkey 
port: p2p端口
validator： 成为验证人
name： 在telemetry中显示的名字，随便写一个
rpc-external： 暴露rpc端口 (可以不填)
ws-external： 暴露ws端口(可以不填)
bootnodes: 连接的节点
```

启动成功后控制台打印日志如下：

```bash
2019-07-02 14:23:49 Darwinia POC-1 Node
2019-07-02 14:23:49   version 0.1.0-f8cb407-x86_64-macos
2019-07-02 14:23:49   by Darwinia Network, 2017-2019
2019-07-02 14:23:49 Chain specification: Darwinia POC-1 Testnet
2019-07-02 14:23:49 Node name: quirky-language-8306
2019-07-02 14:23:49 Roles: AUTHORITY
2019-07-02 14:23:49 Highest known block at #165
2019-07-02 14:23:49 Using default protocol ID "sup" because none is configured in the chain specs
2019-07-02 14:23:49 Local node identity is: QmXk6SfHALGxMpVMMuzEdNkd87WiSdLrPY83q4iSPsetEU
2019-07-02 14:23:49 Libp2p => Random Kademlia query has yielded empty results
2019-07-02 14:23:49 Unable to bind server to 127.0.0.1:9944. Trying random port.
2019-07-02 14:23:49 Using authority key 5GZNC4n5hoYYhZbR8ucYiFjE6mGjVQf87rezViME59u45ATz
2019-07-02 14:23:49 Running Grandpa session as Authority 5GZNC4n5hoYYhZbR8ucYiFjE6mGjVQf87rezViME59u45ATz
2019-07-02 14:23:49 Discovered new external address for our node: /ip4/192.168.110.246/tcp/20224/p2p/QmXk6SfHALGxMpVMMuzEdNkd87WiSdLrPY83q4iSPsetEU
2019-07-02 14:23:54 Idle (2 peers), best: #170 (0x2bac…4414), finalized #170 (0x2bac…4414), ⬇ 4.1kiB/s ⬆ 2.2kiB/s
2019-07-02 14:23:54 Imported #171 (0x466c…5987)
2019-07-02 14:23:59 Idle (2 peers), best: #171 (0x466c…5987), finalized #171 (0x466c…5987), ⬇ 2.4kiB/s ⬆ 1.5kiB/s
2019-07-02 14:24:00 Imported #172 (0x3636…7b06)
2019-07-02 14:24:04 Idle (2 peers), best: #172 (0x3636…7b06), finalized #172 (0x3636…7b06), ⬇ 1.9kiB/s ⬆ 1.3kiB/s
2019-07-02 14:24:06 Imported #173 (0x2ffc…0fbe)
2019-07-02 14:24:09 Idle (2 peers), best: #173 (0x2ffc…0fbe), finalized #173 (0x2ffc…0fbe), ⬇ 2.5kiB/s ⬆ 1.7kiB/s
2019-07-02 14:24:12 Imported #174 (0xaea7…e36e)
2019-07-02 14:24:14 Idle (2 peers), best: #174 (0xaea7…e36e), finalized #173 (0x2ffc…0fbe), ⬇ 1.8kiB/s ⬆ 1.1kiB/s
```

![图片版](https://hammerwang-1255810360.cos.ap-chengdu.myqcloud.com/2019-07-02-065735.jpg)

此时节点仅仅是同步节点。

### 4-4 申请成为验证人

先打开**Staking**页面，查看当前的验证人情况：

![image-20190702142225966](https://hammerwang-1255810360.cos.ap-chengdu.myqcloud.com/2019-07-02-065730.jpg)



打开**Extrinsics -> staking -> validate**, 此时操作账户依然为controller，

- unstakeThreshold: 3 (自己声明的每轮允许漏块的个数)
- validatorPayment: 10（每轮奖励中不愿和人分享的奖励数）

![image-20190702142749967](https://hammerwang-1255810360.cos.ap-chengdu.myqcloud.com/2019-07-02-065731.jpg)

操作之后进入**Staking**界面，可以在 *next up*中看到我们的账户了，说明此时已进入验证人候选名单中，只需要等待下一轮换届即可。

![image-20190702142851451](https://hammerwang-1255810360.cos.ap-chengdu.myqcloud.com/2019-07-02-65732.jpg)



### 4-5 成功当选

页面：

可以看到，节点已经从nextup中变为validators，开始出块了。

![image-20190702143545173](https://hammerwang-1255810360.cos.ap-chengdu.myqcloud.com/2019-07-02-65729.jpg)



在控制台中也可以看到日志的变化：

```bash
2019-07-02 14:31:07 Imported #243 (0x93c5…72f4)
2019-07-02 14:31:09 Idle (2 peers), best: #243 (0x93c5…72f4), finalized #243 (0x93c5…72f4), ⬇ 2.0kiB/s ⬆ 1.3kiB/s
2019-07-02 14:31:12 Imported #244 (0x326a…039d)
2019-07-02 14:31:14 Idle (2 peers), best: #244 (0x326a…039d), finalized #244 (0x326a…039d), ⬇ 2.4kiB/s ⬆ 1.5kiB/s
2019-07-02 14:31:18 Imported #245 (0x7584…71f4)
2019-07-02 14:31:19 Idle (2 peers), best: #245 (0x7584…71f4), finalized #244 (0x326a…039d), ⬇ 1.9kiB/s ⬆ 1.2kiB/s
2019-07-02 14:31:24 Idle (2 peers), best: #245 (0x7584…71f4), finalized #245 (0x7584…71f4), ⬇ 2.2kiB/s ⬆ 1.5kiB/s
2019-07-02 14:31:24 Imported #246 (0x94ba…b514)
2019-07-02 14:31:29 Idle (2 peers), best: #246 (0x94ba…b514), finalized #246 (0x94ba…b514), ⬇ 2.0kiB/s ⬆ 1.3kiB/s
2019-07-02 14:31:30 Imported #247 (0x7ca7…79f8)
2019-07-02 14:31:34 Idle (2 peers), best: #247 (0x7ca7…79f8), finalized #247 (0x7ca7…79f8), ⬇ 2.3kiB/s ⬆ 1.4kiB/s
2019-07-02 14:31:36 Imported #248 (0xa56e…fc33)
2019-07-02 14:31:39 Idle (2 peers), best: #248 (0xa56e…fc33), finalized #248 (0xa56e…fc33), ⬇ 2.1kiB/s ⬆ 1.4kiB/s
2019-07-02 14:31:42 Imported #249 (0x466c…e762)
2019-07-02 14:31:44 Idle (2 peers), best: #249 (0x466c…e762), finalized #248 (0xa56e…fc33), ⬇ 2.0kiB/s ⬆ 1.4kiB/s
2019-07-02 14:31:48 Imported #250 (0xb40f…9a74)
2019-07-02 14:31:49 Idle (2 peers), best: #250 (0xb40f…9a74), finalized #249 (0x466c…e762), ⬇ 2.3kiB/s ⬆ 1.3kiB/s
2019-07-02 14:31:49 Applying authority set change scheduled at block #250
2019-07-02 14:31:49 Applying GRANDPA set change to new set [(d17c2d7823ebf260fd138f2d7e27d114c0145d968b5ff5006125f2414fadae69 (5GoNkf6W...), 1), (88dc3417d5058ec4b4503e0c12ea1a0a89be200fe98922423d4334014fa6b0ee (5FA9nQDV...), 1), (c6ccd9b016cbfea98a9ec36afd36e44d5a95bb4f04fec101591830710d2ea1b9 (5GZNC4n5...), 1)]
2019-07-02 14:31:54 Idle (2 peers), best: #250 (0xb40f…9a74), finalized #250 (0xb40f…9a74), ⬇ 3.8kiB/s ⬆ 3.2kiB/s
2019-07-02 14:31:54 Imported #251 (0x4838…cc2a)
2019-07-02 14:31:59 Idle (2 peers), best: #251 (0x4838…cc2a), finalized #251 (0x4838…cc2a), ⬇ 2.8kiB/s ⬆ 2.5kiB/s
2019-07-02 14:32:00 Starting consensus session on top of parent 0x4838f6add08a482aafc245f33804e65a27d29daf99517aec78888b2d96a2cc2a
2019-07-02 14:32:00 Prepared block for proposing at 252 [hash: 0xe252820aeb3f0375de529cde22b1ade7060bd0d2d36ea5d702a2ab875d90cd09; parent_hash: 0x4838…cc2a; extrinsics: [0xe966…3085, 0xfa45…b192]]
2019-07-02 14:32:00 Pre-sealed block for proposal at 252. Hash now 0x70e904c5917e2b949f749589e2be50c20f15a5956fc1b39c52d2380920af05df, previously 0xe252820aeb3f0375de529cde22b1ade7060bd0d2d36ea5d702a2ab875d90cd09.
2019-07-02 14:32:00 Imported #252 (0x70e9…05df)
2019-07-02 14:32:04 Idle (2 peers), best: #252 (0x70e9…05df), finalized #252 (0x70e9…05df), ⬇ 2.8kiB/s ⬆ 2.8kiB/s
2019-07-02 14:32:06 Imported #253 (0xcdfa…3d68)
2019-07-02 14:32:09 Idle (2 peers), best: #253 (0xcdfa…3d68), finalized #253 (0xcdfa…3d68), ⬇ 2.7kiB/s ⬆ 2.5kiB/s
2019-07-02 14:32:13 Imported #254 (0x6309…2f8b)
2019-07-02 14:32:14 Idle (2 peers), best: #254 (0x6309…2f8b), finalized #254 (0x6309…2f8b), ⬇ 2.6kiB/s ⬆ 2.5kiB/s
2019-07-02 14:32:18 Starting consensus session on top of parent 0x63096d699771487356a8fbc1765bad053536a39b389cb645698acc80a2f42f8b
```

![控制台图片版](https://hammerwang-1255810360.cos.ap-chengdu.myqcloud.com/2019-07-02-065729.jpg)

在`#250`块可以很清晰地看到，之前只是监听同步全网块高，在`#250`之后日志开始验证。



## 有趣的事情

最后在这里介绍达尔文的staking的一些不同之处，达尔文采用二阶锁定机制，即存入RING获得利息KTON，再使用KTON去做Staking，获得系统奖励ring。

在波卡网络中，锁定DOT是存在风险的，即Slash和锁定期的价格波动，在达尔文中，KTON代表着大家对达尔文网络的“类POW”证明，可以看成是权益token。因此即使kton不锁定，也可以获得系统奖励ring的分红，只是数量比做Staking (validate/nominate) 少一些。



下面我们新建一个账号`MAG`，存入一些RING生成KTON之后，可以看到MAG的RING和KTON数量如下（此时MAG并没有做Staking）：

![image-20190702144342142](https://hammerwang-1255810360.cos.ap-chengdu.myqcloud.com/2019-07-02-065734.jpg)

经过一段时间之后，我们可以使用**Extrinsics -> kton -> claimReward** 来领取未锁定KTON的系统奖励，之后再回到**Chain state**中查看MAG当前的RING余额：

![image-20190702145008315](https://hammerwang-1255810360.cos.ap-chengdu.myqcloud.com/2019-07-02-065723.jpg)

可以看到此时MAG的RING变多了一些。

所以，只要存入RING获得KTON之后，即可获得系统的奖励。如果用户把KTON转送他人之后，那么这部分KTON所拥有的的未来的系统分红权，也会转让给其他人。



## 写在最后

达尔文是波卡生态中第一条游戏平行链，关于通证经济设计也有一些有趣的思考。目前的POC-1 Trilobita Testnet只是初期最小化的可用版本。如果大家对我们感兴趣，可以在https://github.com/darwinia-network/rfcs中提issue，也欢迎fork给我们提交PR，共同讨论达尔文网络的未来。



