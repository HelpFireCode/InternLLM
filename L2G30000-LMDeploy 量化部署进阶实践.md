安装环境

```bash
conda create -n lmdeploy  python=3.10 -y
conda activate lmdeploy
conda install pytorch==2.1.2 torchvision==0.16.2 torchaudio==2.1.2 pytorch-cuda=12.1 -c pytorch -c nvidia -y
pip install timm==1.0.8 openai==1.40.3 lmdeploy[all]==0.5.3

pip install datasets==2.19.2
```

![image-20250214150555501](https://typora-urname.oss-cn-beijing.aliyuncs.com/git/202502141528235.png)





进行测试

$1GB=10^3MB=10^6KB=10^9B$

$1B=10^{-3}KB=10^{-6}MB=10^{-9}GB$

现在有$7 \times 10^9B$数量级的模型参数，因此，占用7G，每个参数1B占2个字节，因此占用14G。

验证启动模型文件

生成一个小狐狸和兔子的故事👇

![image-20250214153459377](https://typora-urname.oss-cn-beijing.aliyuncs.com/git/202502141534468.png)

查看显存占用情况👇

![image-20250214153511811](https://typora-urname.oss-cn-beijing.aliyuncs.com/git/202502141535859.png)



启动API服务器，通过sh文件启动👇



