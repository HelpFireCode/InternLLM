# LMDeploy部署

启动环境👇

![image-20250219154601434](https://typora-urname.oss-cn-beijing.aliyuncs.com/git/202502191546562.png)

构造多轮对话代码👇

图片👇

![image-20250219164308667](https://typora-urname.oss-cn-beijing.aliyuncs.com/git/202502191643773.png)

```python
## 1.导入相关依赖包
from lmdeploy import pipeline, TurbomindEngineConfig, GenerationConfig
from lmdeploy.vl import load_image

## 2.使用你的模型初始化推理管线
model_path = "your_model_path"
pipe = pipeline(model_path,
                backend_config=TurbomindEngineConfig(session_len=8192))
                
## 3.读取图片（此处使用PIL读取也行）
image = load_image('your_image_path')

## 4.配置推理参数
gen_config = GenerationConfig(top_p=0.8, temperature=0.8)
## 5.利用 pipeline.chat 接口 进行对话，需传入生成参数
sess = pipe.chat(('描述下这张图片', image), gen_config=gen_config)
print(sess.response.text)
## 6.之后的对话轮次需要传入之前的session，以告知模型历史上下文
sess = pipe.chat('图片中的裤子什么颜色？', session=sess, gen_config=gen_config)
print(sess.response.text)
```

![image-20250219164252795](https://typora-urname.oss-cn-beijing.aliyuncs.com/git/202502191642861.png)

网页应用部署👇

拉取仓库后更换模型文件启动demo文件👇

![image-20250219154801216](https://typora-urname.oss-cn-beijing.aliyuncs.com/git/202502191548277.png)



图片原型为👇

![image-20250219180107293](https://typora-urname.oss-cn-beijing.aliyuncs.com/git/202502191806285.png)

# LMDeploy微调

开始训练👇

![image-20250219185428841](https://typora-urname.oss-cn-beijing.aliyuncs.com/git/202502191854921.png)



训练结束👇

![image-20250219194042368](https://typora-urname.oss-cn-beijing.aliyuncs.com/git/202502191940448.png)

使用命令行部署，此时需要命令👇

```bash
ssh -CNg -L 1096:127.0.0.1:1096 root@ssh.intern-ai.org.cn -p 35345
```

接着复制自己的ssh密码👇

![image-20250219201351028](https://typora-urname.oss-cn-beijing.aliyuncs.com/git/202502192013099.png)

打开浏览器👇

![image-20250219201811679](https://typora-urname.oss-cn-beijing.aliyuncs.com/git/202502192018803.png)

![image-20250219203545346](https://typora-urname.oss-cn-beijing.aliyuncs.com/git/202502192035492.png)

![image-20250219204230697](https://typora-urname.oss-cn-beijing.aliyuncs.com/git/202502192042822.png)