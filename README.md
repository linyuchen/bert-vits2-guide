# bert-vist2的简单部署

***

## 一、下载项目

```shell
git clone https://github.com/fishaudio/Bert-VITS2.git
pip install -r requirements.txt
```

### BERT 模型

放置到`根目录 bert 文件夹`下覆盖

<https://huggingface.co/hfl/chinese-roberta-wwm-ext-large>  
<https://huggingface.co/ku-nlp/deberta-v2-large-japanese-char-wwm>  
<https://huggingface.co/microsoft/deberta-v3-large>

### WavLM 模型

放置到`根目录 slm 文件夹`下覆盖

<https://huggingface.co/microsoft/wavlm-base-plus>

### models 底模

放到 `根目录 data/{你的数据集名称}/models` 文件夹下，需要自己新建

<https://openi.pcl.ac.cn/Stardust_minus/Bert-VITS2/modelmanage/show_model>

## 二、数据集部署

### 1.以下是需要自建或拷贝到这里的文件

```shell
  ├── data （data建在根目录下，不知道大小写有没有关系，之前是Data）
  │   ├── {你的数据集名称}
  │   │   ├── configs （这个文件夹可以复制根目录下的，使用webui生成配置文件后，再拷贝出来）
  │   │   ├── config.json （上面文件夹里的config.json拷贝到这里，训练时用的这个）
  │   │   ├── models （放置4个底膜 D_0.pth、DUR_0.pth、WD_0.pth、G_0.pth）
  │   │   ├── esd.list （示例在下面）
  │   │   ├── raw （里面放分段好的原始音频）
  │   │   │   ├── ****.wav
  │   │   │   ├── ...
```  

* esd.list 文件为标签文本，对应wavs文件夹下的所有文件
* wavs文件夹是音频重采样raw文件夹后自动生成的，训练时会自动用的wavs文件夹  
* esd.list 文件格式： `/data/{你的数据集名称}/wavs/***.wav|{说话人名}|{语言 ID}|{标签文本}`  

* esd.list 文件示例：
  `data/{你的数据集名称}/wavs/paimon_02.wav|派蒙|ZH|没什么没什么，只是平时他总是站在这里，有点奇怪而已。`
  `data/{你的数据集名称}/wavs/noa_51_01.wav|NOA|JP|そうだね、油断しないのはとても大事なことだと思う`
  `data/{你的数据集名称}/wavs/albedo_01.wav|Albedo|EN|Who are you? Why did you alarm them?`

### 2. 修改根目录下的 `config.yml` 中 `dataset_path` 一项为 `data/{你的数据集名称}`

### 3. 上面大概操作流程（可以不按步骤来）

* 运行：`python webui_preprocess.py`
* 第一步：生成配置文件（）
* 第二步：预处理音频文件（）
* 第三步：预处理标签文件（）
* 第四步：生成 BERT 特征文件（）

## 三、训练

* 训练执行 `torchrun --nproc_per_node=1 train_ms.py` 命令，或直接运行`train_ms.py`
* `config.json`文件里的`eval_interval`是间隔多少保存模型，`epochs`是本次总共训练多少次，其他还不清楚喵
* 多卡运行可参考 `run_MnodesAndMgpus.sh` 中的命令  

## 四、部署

1. 修改根目录下的` config.yml `中 `webui` 下 `model` 一项为 `models/{权重文件名}.pth`（如 G_10000.pth）
2. 然后执行 `python webui.py` 部署

## 注意事项

### 项目依赖不支持```python 3.12```
