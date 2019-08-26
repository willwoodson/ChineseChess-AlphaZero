# 中国象棋Zero（CCZero）

<a href="https://cczero.org">
  <img src="https://cczero.org/zero2.png" alt="App Icon" />
</a>

## About 

Chinese Chess reinforcement learning by [AlphaZero](https://arxiv.org/abs/1712.01815) methods.
使用AlphaZero方法的中国象棋强化学习。

This project is based on these main resources:
该项目以下主要资源为基础：

1. DeepMind's Oct 19th publication: [Mastering the Game of Go without Human Knowledge](https://www.nature.com/articles/nature24270.epdf?author_access_token=VJXbVjaSHxFoctQQ4p2k4tRgN0jAjWel9jnR3ZoTv0PVW4gB86EEpGqTRDtpIz-2rmo8-KG06gqVobU5NSCFeHILHcVFUeMsbvwS-lxjqQGg98faovwjxeTUgZAUMnRQ).
2. The **great** Reversi/Chess/Chinese chess development of the DeepMind ideas that @mokemokechicken/@Akababa/@TDteach did in their repo: https://github.com/mokemokechicken/reversi-alpha-zero, https://github.com/Akababa/Chess-Zero, https://github.com/TDteach/AlphaZero_ChineseChess
3. A Chinese chess engine with gui: https://github.com/mm12432/MyChess


## Help to train

In order to build a strong chinese chess AI following the same type of techniques as AlphaZero, we need to do this with a distributed project, as it requires a huge amount of computations.
为了按照与AlphaZero相同的技术构建一个强大的中国象棋AI，我们需要使用一个分布式项目来实现这一点，因为它需要大量的计算。

If you want to join us to build the best chinese chess AI in the world:
如果你想和我们一起打造世界上最好的中国象棋AI：

* For instructions, see [wiki](https://github.com/NeymarL/ChineseChess-AlphaZero/wiki)
* For live status, see https://cczero.org

![elo](elo.png)


## Environment

* Python 3.6.3
* tensorflow-gpu: 1.3.0
* Keras: 2.0.8


## Modules

### Reinforcement Learning

This AlphaZero implementation consists of two workers: `self` and  `opt`.
这一AlphaZero实现由两名工作人员组成

* `self` is Self-Play to generate training data by self-play using BestModel.
  是自我游戏，用最好的模型通过自我游戏生成训练数据。
* `opt` is Trainer to train model, and generate new models.
  是培训师，用来训练模型，并产生新的模型。

For the sake of faster training, another two workers are involved:
为了更快地进行培训，另有两名工人参加了培训：

* `sl` is Supervised learning to train data crawled from the Internet.
  是在监督下学习训练从互联网上爬来的数据。
* `eval` is Evaluator to evaluate the NextGenerationModel with the current BestModel.
  是用当前最佳模型评估NextGenerationModel的评估器。
### Built-in GUI

Requirement: pygame

```bash
python cchess_alphazero/run.py play
```

**Screenshots**

![board](screenshots/board.png)

You can choose different board/piece styles and sides, see [play with human](#play-with-human).


## How to use

### Setup

### install libraries
```bash
pip install -r requirements.txt
```

If you want to use CPU only, replace `tensorflow-gpu` with `tensorflow` in `requirements.txt`.

Make sure Keras is using Tensorflow and you have Python 3.6.3+.

### Configuration

**PlayDataConfig**

* `nb_game_in_file, max_file_num`: The max game number of training data is `nb_game_in_file * max_file_num`.
训练数据的最大博弈数是 `nb_game_in_file * max_file_num`。

**PlayConfig, PlayWithHumanConfig**

* `simulation_num_per_move` : MCTS number per move.
  MCTS每次移动数
* `c_puct`: balance parameter of value network and policy network in MCTS.
  MCTS中价值网络和策略网络的平衡参数
* `search_threads`: balance parameter of speed and accuracy in MCTS.
  MCTS中速度和精度的平衡参数。
* `dirichlet_alpha`: random parameter in self-play.
  自演中的随机参数。

### Full Usage

```
usage: run.py [-h] [--new] [--type TYPE] [--total-step TOTAL_STEP]
              [--ai-move-first] [--cli] [--gpu GPU] [--onegreen] [--skip SKIP]
              [--ucci] [--piece-style {WOOD,POLISH,DELICATE}]
              [--bg-style {CANVAS,DROPS,GREEN,QIANHONG,SHEET,SKELETON,WHITE,WOOD}]
              [--random {none,small,medium,large}] [--distributed] [--elo]
              {self,opt,eval,play,eval,sl,ob}

positional arguments:
  {self,opt,eval,play,eval,sl,ob}
                        what to do

optional arguments:
  -h, --help            show this help message and exit
  --new                 run from new best model
  --type TYPE           use normal setting
  --total-step TOTAL_STEP
                        set TrainerConfig.start_total_steps
  --ai-move-first       set human or AI move first
  --cli                 play with AI with CLI, default with GUI
  --gpu GPU             device list
  --onegreen            train sl work with onegreen data
  --skip SKIP           skip games
  --ucci                play with ucci engine instead of self play
  --piece-style {WOOD,POLISH,DELICATE}
                        choose a style of piece
  --bg-style {CANVAS,DROPS,GREEN,QIANHONG,SHEET,SKELETON,WHITE,WOOD}
                        choose a style of board
  --random {none,small,medium,large}
                        choose a style of randomness
  --distributed         whether upload/download file from remote server
  --elo                 whether to compute elo score
```

### Self-Play

```
python cchess_alphazero/run.py self
```

When executed, self-play will start using BestModel. If the BestModel does not exist, new random model will be created and become BestModel. Self-play records will store in `data/play_record` and BestMode will store in `data/model`.
执行时，自我博弈 将开始使用BestModel。如果最佳模型不存在，将创建新的随机模型并成为最佳模型。自我博弈 记录将存储在`data/play_record`，最佳模式将存储在`data/model`。

options

* `--new`: create new BestModel
* `--type mini`: use mini config, (see `cchess_alphazero/configs/mini.py`)
* `--gpu '1'`: specify which gpu to use 指定要使用哪个GPU
* `--ucci`: whether to play with ucci engine (rather than self play, see `cchess_alphazero/worker/play_with_ucci_engine.py`)
  是否使用UCCI引擎(而不是自我博弈，请参见`cchess_alphazero/worker/play_with_ucci_engine.py`)
* `--distributed`: run self play in distributed mode which means it will upload the play data to the remote server and download latest model from it
  在分布式模式下运行自我博弈，这意味着它将向远程服务器上传播放数据并从其下载最新模型

**Note1**: To help training, you should run `python cchess_alphazero/run.py --type distribute --distributed self` (and do not change the configuration file `configs/distribute.py`), for more info, see [wiki](https://github.com/NeymarL/ChineseChess-AlphaZero/wiki/For-Developers).

**Note2**: If you want to view the self-play records in GUI, see [wiki](https://github.com/NeymarL/ChineseChess-AlphaZero/wiki/View-self-play-games-in-GUI).

### Trainer

```
python cchess_alphazero/run.py opt
```

When executed, Training will start. The current BestModel will be loaded. Trained model will be saved every epoch as new BestModel.
一旦执行，训练就开始了。目前最好的型号将载入。受过训练的模特将被保存成新的最佳模式。

options

* `--type mini`: use mini config, (see `cchess_alphazero/configs/mini.py`)
* `--total-step TOTAL_STEP`: specify total step(mini-batch) numbers. The total step affects learning rate of training.
  指定总步长(小批次)编号.总步数影响训练的学习率。
* `--gpu '1'`: specify which gpu to use

**View training log in Tensorboard**

```
tensorboard --logdir logs/
```

And access `http://<The Machine IP>:6006/`.

### Play with human

**Run with built-in GUI**

```
python cchess_alphazero/run.py play
```

When executed, the BestModel will be loaded to play against human.
执行时，将加载BestModel以对抗人类。

options

* `--ai-move-first`: if set this option, AI will move first, otherwise human move first.
如果设置此选项，AI将首先移动，否则人类首先移动。
* `--type mini`: use mini config, (see `cchess_alphazero/configs/mini.py`)
* `--gpu '1'`: specify which gpu to use
* `--piece-style WOOD`: choose a piece style, default is `WOOD`
选择一个片段样式，默认为 `WOOD`
* `--bg-style CANVAS`: choose a board style, default is `CANVAS`
选择板式，默认为 `CANVAS`
* `--cli`: if set this flag, play with AI in a cli environment rather than gui
如果设置此标志，则在cli环境中而不是gui中使用AI。

**Note**: Before you start, you need to download/find a font file (`.ttc`) and rename it as `PingFang.ttc`, then put it into `cchess_alphazero/play_games`. I have removed the font file from this repo because it's too big, but you can download it from [here](http://alphazero.52coding.com.cn/PingFang.ttc).
在开始之前，您需要下载/查找一个字体文件`.ttc`并重命名为`PingFang.ttc`，然后把他放在`cchess_alphazero/play_games`，我已从该回购中删除了字体文件，因为它太大了，但您可以从[此处下载](http://alphazero.52coding.com.cn/PingFang.ttc)

You can also download Windows executable directly from [here](https://pan.baidu.com/s/1uE_zmkn0x9Be_olRL9U9cQ). For more information, see [wiki](https://github.com/NeymarL/ChineseChess-AlphaZero/wiki/For-Non-Developers#%E4%B8%8B%E6%A3%8B).

**UCI mode**

```
python cchess_alphazero/uci.py
```

If you want to play in general GUIs such as '冰河五四', you can download the Windows executable [here](https://share.weiyun.com/5cK50Z4). For more information, see [wiki](https://github.com/NeymarL/ChineseChess-AlphaZero/wiki/For-Non-Developers#%E4%B8%8B%E6%A3%8B).
如果您想在普通GUI(如“冰河五四”)中播放，您可以在这里下载Windows可执行文件。有关更多信息，请参见wiki。

### Evaluator

```
python cchess_alphazero/run.py eval
```

When executed, evaluate the NextGenerationModel with the current BestModel. If the NextGenerationModel does not exist, worker will wait until it exists and check every 5 minutes.
执行时，使用当前BestModel评估NextGenericationModel。如果NextGenericationModel不存在，则员工将等待直到它存在并每隔5分钟进行一次检查。

options

* `--type mini`: use mini config, (see `cchess_alphazero/configs/mini.py`)
* `--gpu '1'`: specify which gpu to use

### Supervised Learning 有监督的学习

```
python cchess_alphazero/run.py sl
```

When executed, Training will start. The current SLBestModel will be loaded. Tranined model will be saved every epoch as new SLBestModel.
一旦执行，训练就开始了。将加载当前的SLBestModel。经过训练的模型将作为新的SLBestModel保存每一个时代。

*About the data*

I have two data sources, one is downloaded from https://wx.jcloud.com/market/packet/10479 ; the other is crawled from http://game.onegreen.net/chess/Index.html (with option --onegreen).

options

* `--type mini`: use mini config, (see `cchess_alphazero/configs/mini.py`)
* `--gpu '1'`: specify which gpu to use
* `--onegreen`: if set the flag, `sl_onegreen` worker will start to train data crawled from `game.onegreen.net`
if set the flag，`sl_onegreen`工人将从`game.onegreen.net`开始训练数据
* `--skip SKIP`: if set this flag, games whoses index is less than `SKIP` would not be used to train (only valid when `onegreen` flag is set)
如果设置此标志，则索引小于`SKIP`不用于训练（仅当设置`onegreen`标志时有效）
