# 安装环境

```bash
conda create -n lmdeploy  python=3.10 -y
conda activate lmdeploy
conda install pytorch==2.1.2 torchvision==0.16.2 torchaudio==2.1.2 pytorch-cuda=12.1 -c pytorch -c nvidia -y
pip install timm==1.0.8 openai==1.40.3 lmdeploy[all]==0.5.3

pip install datasets==2.19.2
```

![image-20250214150555501](https://typora-urname.oss-cn-beijing.aliyuncs.com/git/202502141528235.png)





# 进行测试

$1GB=10^3MB=10^6KB=10^9B$

$1B=10^{-3}KB=10^{-6}MB=10^{-9}GB$

现在有$7 \times 10^9B$数量级的模型参数，因此，占用7G，每个参数1B占2个字节，因此占用14G。

验证启动模型文件

# 生成一个小狐狸和兔子的故事👇

![image-20250214153459377](https://typora-urname.oss-cn-beijing.aliyuncs.com/git/202502141534468.png)

## 查看显存占用情况👇

![image-20250214153511811](https://typora-urname.oss-cn-beijing.aliyuncs.com/git/202502141535859.png)



## 启动API服务器，通过sh文件启动👇

![image-20250214172437263](https://typora-urname.oss-cn-beijing.aliyuncs.com/git/20250214172437295.png)

![image-20250214172253236](https://typora-urname.oss-cn-beijing.aliyuncs.com/git/20250214172253317.png)

以命令行形式连接服务器👇

![image-20250214174901335](https://typora-urname.oss-cn-beijing.aliyuncs.com/git/20250214174901366.png)

## 使用gradio运行👇

![image-20250214175206413](https://typora-urname.oss-cn-beijing.aliyuncs.com/git/20250214175206475.png)

## 在不使用kv cache时的显存占用为👇

![image-20250214175605749](https://typora-urname.oss-cn-beijing.aliyuncs.com/git/20250214175605789.png)

使用`lmdeploy chat /root/models/internlm2_5-7b-chat --cache-max-entry-count 0.4`后的显存占用为👇

![image-20250214175915187](https://typora-urname.oss-cn-beijing.aliyuncs.com/git/20250214175915224.png)

可以看到有明显的下降，大约减少4GB的显存占用。



---

# 设置在线kv cache int4 量化👇

## 脚本命令👇

```bash
lmdeploy serve api_server \
    /root/models/internlm2_5-7b-chat \
    --model-format hf \
    --quant-policy 4 \
    --cache-max-entry-count 0.4\
    --server-name 0.0.0.0 \
    --server-port 23333 \
    --tp 1
```

## 执行结果查看显存大小👇

![image-20250214181730448](https://typora-urname.oss-cn-beijing.aliyuncs.com/git/20250214181730478.png)

## 使用命令行连接api👇

![image-20250214181754547](https://typora-urname.oss-cn-beijing.aliyuncs.com/git/20250214181754583.png)

## 使用gradio连接api👇

![image-20250214182541280](https://typora-urname.oss-cn-beijing.aliyuncs.com/git/20250214182541332.png)

![image-20250214182552478](https://typora-urname.oss-cn-beijing.aliyuncs.com/git/20250214182552555.png)



---



# 执行量化操作👇

![image-20250214182523637](https://typora-urname.oss-cn-beijing.aliyuncs.com/git/20250214182523712.png)

## 量化结束后启动模型👇

`lmdeploy chat /root/models/internlm2_5-7b-chat-w4a16-4bit/ --model-format awq`

结果👇

![image-20250214192733753](https://typora-urname.oss-cn-beijing.aliyuncs.com/git/20250214192733810.png)



## 此时的显存占用👇

![image-20250214192812792](https://typora-urname.oss-cn-beijing.aliyuncs.com/git/20250214192812815.png)



## 模型量化结束👇

![image-20250219004044223](https://typora-urname.oss-cn-beijing.aliyuncs.com/git/202502190040350.png)

启用量化后的模型后，此时显存占用为👇

![image-20250219004235388](https://typora-urname.oss-cn-beijing.aliyuncs.com/git/202502190042458.png)

可以看到用到了23G

![image-20250219004950193](https://typora-urname.oss-cn-beijing.aliyuncs.com/git/202502190049255.png)



# FunctionCall👇



出现问题，说是有不期望的初始值。更换版本就好了。参考链接[^openai]。

![image-20250219010607591](https://typora-urname.oss-cn-beijing.aliyuncs.com/git/202502190106663.png)

![image-20250219010616524](https://typora-urname.oss-cn-beijing.aliyuncs.com/git/202502190106586.png)



![image-20250219010526071](https://typora-urname.oss-cn-beijing.aliyuncs.com/git/202502190105163.png)



## 4.2 function call

函数调用功能，允许开发者调用模型时，详细说明函数的作用，并使模型能够智能根据用户提问输入参数并执行函数。完成调用后，模型会根据函数的输出要求作为输出结果。

![image-20250219011638228](https://typora-urname.oss-cn-beijing.aliyuncs.com/git/202502190116327.png)

可以看到分别调用了加法和乘法函数。

