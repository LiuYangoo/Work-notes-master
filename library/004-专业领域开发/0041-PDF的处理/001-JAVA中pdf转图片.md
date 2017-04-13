#   JAVA中pdf转图片
>维护人员：**liuyang**  
>创建时间：2017-03-16   


　　最近项目中有个需求需要把PDF转成一张图。经过查找，有三款比较流行的Java开源软件有这个功能。但在使用过程中， 它们的区别还是很大的。

　　1.PDFRenderer： 确实效率最高，但是缺少字体支持对大多数中文pdf处理不了,而且对于比较大的pdf有局限。

　　2.pdfbox：字体基本都可以转换，但容易内存溢出

　　3.jpedal：效率不错。中文pdf文件就是生成缩略图不对。

    最后我选择了icepdf，支持大多pdf,较大的pdf也可使用，应用面积比较广。

需要jar:icepdf-core.jar、icepdf-viewer.jar 　　　　[下载地址](http://www.icesoft.org/java/downloads/icepdf-downloads.jsf "下载地址")



源码如下:
```Java
import java.awt.image.BufferedImage;
import java.io.File;
import java.io.FileOutputStream;
import java.io.IOException;
import java.util.Iterator;

import javax.imageio.IIOImage;
import javax.imageio.ImageIO;
import javax.imageio.ImageWriter;
import javax.imageio.stream.ImageOutputStream;

import org.icepdf.core.exceptions.PDFException;
import org.icepdf.core.exceptions.PDFSecurityException;
import org.icepdf.core.pobjects.Document;
import org.icepdf.core.pobjects.Page;
import org.icepdf.core.util.GraphicsRenderingHints;



/**
 *
 * @author hubiao
 * @dateTime 2014-06-07
 *		本工具对实现对IMG与PDF相互转换。
 *		运行测试需要导入以下2个jar包
 *		icepdf-core.jar
 *    icepdf-viewer.jar
 *
 */

public class ImgPdfUtils {
	public static void main(String[] args) throws Exception {
		int a=tranfer("e:/文史资料第一辑.pdf","e:/test111111/",1);
	}

	public static final String FILETYPE_JPG = "jpg";
	public static final String SUFF_IMAGE = "." + FILETYPE_JPG;

/**
	 * 将指定pdf文件的首页转换为指定路径的缩略图
	 *@param filepath 原文件路径，例如d:/test.pdf
	 *@param imagepath 图片生成路径，例如 d:/test-1.jpg
	 *@param zoom     缩略图显示倍数，1表示不缩放，0.3则缩小到30%
	 */
	public static int tranfer(String filepath, String imagepath, float zoom)
			throws PDFException, PDFSecurityException, IOException {
		// ICEpdf document class
		Document document = null;

		float rotation = 0f;

		document = new Document();
		document.setFile(filepath+"/file/"+imagepath);
		int maxPages = document.getPageTree().getNumberOfPages();
           System.out.println("%%%%%%%%%%%%%"+maxPages);

           String name=imagepath.substring(imagepath.indexOf("/") + 1,imagepath.indexOf("."));
	        String getPdfFilePath =filepath+"/pdfPicFile/"+name;
           File files = new File(getPdfFilePath);
           if (!files.exists()) {
           	   files.mkdirs();
           }

         for(int i=0;i<maxPages;i++){
				BufferedImage img = (BufferedImage) document.getPageImage(i,
						GraphicsRenderingHints.SCREEN, Page.BOUNDARY_CROPBOX, rotation,
						zoom);

				Iterator iter = ImageIO.getImageWritersBySuffix(FILETYPE_JPG);
				ImageWriter writer = (ImageWriter) iter.next();
				File outFile = new File(getPdfFilePath+"/"+(i+1)+".jpg");
				FileOutputStream out = new FileOutputStream(outFile);
				ImageOutputStream outImage = ImageIO.createImageOutputStream(out);
				writer.setOutput(outImage);
				writer.write(new IIOImage(img, null, null));
         }
         return maxPages;
	}

}

```
