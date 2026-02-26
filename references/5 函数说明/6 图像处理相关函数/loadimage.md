# loadimage

这个函数用于从文件中读取图像。
    
    
    // 从图片文件获取图像(bmp/gif/jpg/png/tif/emf/wmf/ico)
    int loadimage(
    	IMAGE* pDstImg,			// 保存图像的 IMAGE 对象指针
    	LPCTSTR pImgFile,		// 图片文件名
    	int nWidth = 0,			// 图片的拉伸宽度
    	int nHeight = 0,		// 图片的拉伸高度
    	bool bResize = false	// 是否调整 IMAGE 的大小以适应图片
    );
    
    
    
    // 从资源文件获取图像(bmp/gif/jpg/png/tif/emf/wmf/ico)
    int loadimage(
    	IMAGE* pDstImg,			// 保存图像的 IMAGE 对象指针
    	LPCTSTR pResType,		// 资源类型
    	LPCTSTR pResName,		// 资源名称
    	int nWidth = 0,			// 图片的拉伸宽度
    	int nHeight = 0,		// 图片的拉伸高度
    	bool bResize = false	// 是否调整 IMAGE 的大小以适应图片
    );
    

## 参数

### pDstImg

保存图像的 IMAGE 对象指针。如果为 NULL，表示图片将读取至绘图窗口。

### pImgFile

图片文件名。支持 bmp / gif / jpg / png / tif / emf / wmf / ico 格式的图片。gif 格式的图片仅加载第一帧；gif 与 png 均不支持透明。

### nWidth

图片的拉伸宽度。加载图片后，会拉伸至该宽度。如果为 0，表示使用原图的宽度。

### nHeight

图片的拉伸高度。加载图片后，会拉伸至该高度。如果为 0，表示使用原图的高度。

### bResize

是否调整 IMAGE 的大小以适应图片。

### pResType

图片资源类型。

### pResName

图片资源名称。

## 返回值

返回值表示图片文件资源的存在状态，有三种值：

宏 | 值 | 含义  
---|---|---  
NO_ERROR | 0 | 文件存在  
ERROR_FILE_NOT_FOUND | 2 | 文件没找到  
ERROR_RESOURCE_NOT_FOUND | 5007 | 资源没找到  
  
备注

如果创建 IMAGE 对象的时候没有指定宽高，可以通过 Resize 函数设置。

对于没有设置宽高的 IMAGE 对象，执行 loadimage 会将其宽高设置为和读取的图片一样的尺寸。

## 示例

以下完整示例实现了加载图片“D:\test.jpg”至绘图窗口：
    
    
    #include <graphics.h>
    #include <conio.h>
    
    // 主函数
    int main()
    {
    	// 绘图窗口初始化
    	initgraph(640, 480);
    
    	// 读取图片至绘图窗口
    	loadimage(NULL, _T("D:\\test.jpg"));
    
    	// 按任意键退出
    	_getch();
    	closegraph();
    }
    

以下代码片段实现了加载图片“D:\test.jpg”至绘图窗口（MBCS 及 Unicode 自适应）：
    
    
    loadimage(NULL, _T("D:\\test.jpg"));
    

以下代码片段实现了加载“IMAGE”分类下的图像资源“Player”至 img 对象，并在屏幕的指定位置显示：
    
    
    IMAGE img;
    loadimage(&img, _T("IMAGE"), _T("Player"));
    putimage(100, 100, &img);	// 在另一个位置再次显示背景
    

以下代码片段实现了加载“IMAGE”分类下的图像资源 IDR_PLAYER 至 img 对象，并在屏幕的指定位置显示：
    
    
    IMAGE img;
    loadimage(&img, _T("IMAGE"), MAKEINTRESOURCE(IDB_PLAYER));
    putimage(100, 100, &img);	// 在另一个地方再次显示背景
    
