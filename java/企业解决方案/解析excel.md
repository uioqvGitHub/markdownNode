[TOC]

## excel导入、导出

解析：POI

```
本地---数据库
```

导入：

```
1：读取本地excel文件
2：将文件上传到应用服务器tomcat
3：读取服务器中的excel文件数据，并将数据存放到数据库
```

导出：

```
数据库---本地

1：获取要导出的数据
2：在应用服务器中创建一个空的excel文件，并将导出的数据写入
3：将应用服务器中excel文件，下载本地
```
##api版本问题

HSSFWorkBook：操作2003版本以前的（包括2003版本），扩展名.xls，该类在org.apache.poi:poi中

XSSFWorkBook：操作2007版本以后的（包括2007版本），拓展名.xlsx，该类在org.apache.poi:poi-ooxml中

SXSSFWorkBook：对于海量的数据进行操作

对于不同版本的EXCEL文档要使用不同的工具类，如果使用错了，会提示如下错误信息。

org.apache.poi.openxml4j.exceptions.InvalidOperationException

org.apache.poi.poifs.filesystem.OfficeXmlFileException

## HSSF对xls后缀名的Excel进行读取内容:

```java
import org.apache.poi.hssf.usermodel.HSSFCell;  
import org.apache.poi.hssf.usermodel.HSSFRow;  
import org.apache.poi.hssf.usermodel.HSSFSheet;  
import org.apache.poi.hssf.usermodel.HSSFWorkbook;  
import org.apache.poi.poifs.filesystem.POIFSFileSystem;  
import org.apache.poi.ss.usermodel.CellStyle;  
import org.apache.poi.ss.usermodel.CellType;  
  
import java.io.File;  
import java.io.FileInputStream;  
import java.io.IOException;  
  
/** 
 * Created by M_WBCG on 2017/7/14. 
 */  
public class ReadExcelForHSSF {  
  
    public void read() {  
        File file = new File("././POI/POI1.xls");  
        if (!file.exists())  
            System.out.println("文件不存在");  
        try {  
            //1.读取Excel的对象  
            POIFSFileSystem poifsFileSystem = new POIFSFileSystem(new FileInputStream(file));  
            //2.Excel工作薄对象  
            HSSFWorkbook hssfWorkbook = new HSSFWorkbook(poifsFileSystem);  
            //3.Excel工作表对象  
            HSSFSheet hssfSheet = hssfWorkbook.getSheetAt(0);  
            //总行数  
            int rowLength = hssfSheet.getLastRowNum()+1;  
            //4.得到Excel工作表的行  
            HSSFRow hssfRow = hssfSheet.getRow(0);  
            //总列数  
            int colLength = hssfRow.getLastCellNum();  
            //得到Excel指定单元格中的内容  
            HSSFCell hssfCell = hssfRow.getCell(0);  
            //得到单元格样式  
            CellStyle cellStyle = hssfCell.getCellStyle();  
  
            for (int i = 0; i < rowLength; i++) {  
                //获取Excel工作表的行  
                HSSFRow hssfRow1 = hssfSheet.getRow(i);  
                for (int j = 0; j < colLength; j++) {  
                    //获取指定单元格  
                    HSSFCell hssfCell1 = hssfRow1.getCell(j);  
  
                    //Excel数据Cell有不同的类型，当我们试图从一个数字类型的Cell读取出一个字符串时就有可能报异常：  
                    //Cannot get a STRING value from a NUMERIC cell  
                    //将所有的需要读的Cell表格设置为String格式  
                    if (hssfCell1 != null) {  
                        hssfCell1.setCellType(CellType.STRING);  
                    }  
  
                    //获取每一列中的值  
                    System.out.print(hssfCell1.getStringCellValue() + "\t");  
                }  
                System.out.println();  
            }  
        } catch (IOException e) {  
            e.printStackTrace();  
        }  
    }  
  
    public static void main(String[] args) {  
        new ReadExcelForHSSF().read();  
    }     
}
```

## XSSF对xlsx后缀名的Excel进行读取内容：

```java
import org.apache.poi.ss.usermodel.*;  
  
import java.io.*;  
  
/** 
 * Created by M_WBCG on 2017/7/14. 
 */  
public class ReadExcelForXSSF {  
    public void read() {  
        File file = new File("././POI/POI2.xlsx");  
        InputStream inputStream = null;  
        Workbook workbook = null;  
        try {  
            inputStream = new FileInputStream(file);  
            workbook = WorkbookFactory.create(inputStream);  
            inputStream.close();  
            //工作表对象  
            Sheet sheet = workbook.getSheetAt(0);  
            //总行数  
            int rowLength = sheet.getLastRowNum()+1;  
            //工作表的列  
            Row row = sheet.getRow(0);  
            //总列数  
            int colLength = row.getLastCellNum();  
            //得到指定的单元格  
            Cell cell = row.getCell(0);  
            //得到单元格样式  
            CellStyle cellStyle = cell.getCellStyle();  
            System.out.println("行数：" + rowLength + ",列数：" + colLength);  
            for (int i = 0; i < rowLength; i++) {  
                row = sheet.getRow(i);  
                for (int j = 0; j < colLength; j++) {  
                    cell = row.getCell(j);  
                    //Excel数据Cell有不同的类型，当我们试图从一个数字类型的Cell读取出一个字符串时就有可能报异常：  
                    //Cannot get a STRING value from a NUMERIC cell  
                    //将所有的需要读的Cell表格设置为String格式  
                    if (cell != null)  
                        cell.setCellType(CellType.STRING);  
  
                    //对Excel进行修改  
                    if (i > 0 && j == 1)  
                        cell.setCellValue("1000");  
                    System.out.print(cell.getStringCellValue() + "\t");  
                }  
                System.out.println();  
            }  
  
            //将修改好的数据保存  
            OutputStream out = new FileOutputStream(file);  
            workbook.write(out);  
        } catch (Exception e) {  
            e.printStackTrace();  
        }  
    }  
  
    public static void main(String[] args) {  
        new ReadExcelForXSSF().read();  
    }  
}  
```

## 对Excel写入内容使用的XSSF

```java
import org.apache.poi.hssf.util.HSSFColor;  
import org.apache.poi.ss.usermodel.*;  
import org.apache.poi.xssf.usermodel.XSSFWorkbook;  
  
import java.io.*;  
  
/** 
 * Created by M_WBCG on 2017/7/14. 
 */  
public class WriteExcelForXSSF {  
    public void write() {  
        Workbook workbook = new XSSFWorkbook();  
        Sheet sheet = workbook.createSheet("0");  
        Row row = sheet.createRow(0);  
        CellStyle cellStyle = workbook.createCellStyle();  
        // 设置这些样式  
        cellStyle.setFillForegroundColor(HSSFColor.SKY_BLUE.index);  
        cellStyle.setFillPattern(FillPatternType.SOLID_FOREGROUND);  
        cellStyle.setBorderBottom(CellStyle.BORDER_THIN);  
        cellStyle.setBorderLeft(CellStyle.BORDER_THIN);  
        cellStyle.setBorderRight(CellStyle.BORDER_THIN);  
        cellStyle.setBorderTop(CellStyle.BORDER_THIN);  
        cellStyle.setAlignment(CellStyle.ALIGN_CENTER);  
  
        row.createCell(0).setCellStyle(cellStyle);  
        row.createCell(0).setCellValue("姓名");  
  
        row.createCell(1).setCellStyle(cellStyle);  
        row.createCell(1).setCellValue("年龄");  
  
        workbook.setSheetName(0, "信息");  
        try {  
            File file = new File("././POI/POI3.xlsx");  
            FileOutputStream fileoutputStream = new FileOutputStream(file);  
            workbook.write(fileoutputStream);  
            fileoutputStream.close();  
        } catch (IOException e) {  
            e.printStackTrace();  
        }  
    }  
  
    public static void main(String[] args) {  
        new WriteExcelForXSSF().write();  
    }  
}  
```

