## 配置一个 JFinal 项目

## Jfinal Post 限制上传大小为 10M
解决方法：
1. 重写 JFinalConfig 中的 configConstant 方法，设置 maxPostSize。
```java
@Override
	public void configConstant(Constants arg0)
	{
		arg0.setMaxPostSize(100*Const.DEFAULT_MAX_POST_SIZE);
	}
```  
- 通过 Controller 获取文件或者文件列表时，设置 maxPostSize。
```java  
mController.getFile(uploadPath, maxPostSize);
mController.getFile(uploadPath, maxPostSize, encoding);
mController.getFiles(uploadPath, maxPostSize);
mController.getFiles(uploadPath, maxPostSize, encoding);  
```