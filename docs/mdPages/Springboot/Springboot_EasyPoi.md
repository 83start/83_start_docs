
# 1 Apache POI是什么？

Apache POI官网: https://poi.apache.org/

![img](https://halo-83-start.oss-cn-shenzhen.aliyuncs.com/file/image/typora-202203311244452.png)

# 2 EasyExcel是什么？

 官方文档: https://www.yuque.com/easyexcel/doc/easyexcel

![在这里插入图片描述](https://halo-83-start.oss-cn-shenzhen.aliyuncs.com/file/image/typora-202203311244900.png)
EasyExcel是阿里巴巴开源的一个excel处理框架，以使用简单，节约内存著称。
优势：EasyExcel能大量减少占用内存的主要原因是在解析Excel时没有将文件数据一次性全部加载到内存中，而是从一个磁盘上一行行读取数据，逐个解析。
下图是EasyExcel和POI在解析Excel时的对比图(时间与空间的相互取舍)。

![在这里插入图片描述](https://halo-83-start.oss-cn-shenzhen.aliyuncs.com/file/image/typora-202203311244458.png)

# 3 Apache POI操作？

    HSSF：Excel97-2003版本，扩展名为.xls。一个sheet最大行数65536，最大列数256。
    
    XSSF：Excel2007版本开始，扩展名为.xlsx。一个sheet最大行数1048576，最大列数16384。
    
    SXSSF：是在XSSF基础上，POI3.8版本开始提供的支持低内存占用的操作方式，扩展名为.xlsx。

## 3.2 POI写操作

```xml
创建一个普通空项目empty model,然后新建一个model的maven项目
引入pom依赖

<!--导入依赖-->
<dependencies>
	<!--xLs(03)-->
	<dependency>
		<groupId>org.apache.poi</groupId>
		<artifactId>poi</artifactId>
		<version >3.9</version>
	</dependency>
	<!--xLsx(07)-->
	 <dependency>
        <groupId>org.apache.poi</groupId>
        <artifactId>poi-ooxml</artifactId>
        <version>3.9</version>
     </dependency>
	<!--日期格式化工具-->
	<dependency>
		<groupId>joda-time</groupId>
		<artifactId>joda-time</artifactId>
		<version>2.10.1</version>
	</dependency>
	<!--test-->
	<dependency>
		<groupId>junit</groupId>
		<artifactId>junit</artifactId>
		<version>4.12</version>
	</dependency>
</dependencies>
```

> 注：03|07版本的写，就是对象不同，方法是一样的
>
> 区别：03版最多65536行，07行数没有限制 ，03  HSSFWorkbook() 后缀xls，07 XSSFWorkbook() 后缀xlsx，

![在这里插入图片描述](https://halo-83-start.oss-cn-shenzhen.aliyuncs.com/file/image/typora-202203311245428.png)

- 工作薄
- 工作表
- 行
- 单元格

> 03版本

```java
package com.kuang;
 
import org.apache.poi.hssf.usermodel.HSSFWorkbook;
import org.apache.poi.ss.usermodel.Cell;
import org.apache.poi.ss.usermodel.Row;
import org.apache.poi.ss.usermodel.Sheet;
import org.apache.poi.ss.usermodel.Workbook;
import org.joda.time.DateTime;
import org.junit.Test;
 
import java.io.FileOutputStream;
import java.io.IOException;
import java.util.Date;
 
public class ExcelWriteTest {
 
    String PATH="ExcelCreate\\";
 
    @Test
    public void testWrite03() throws IOException {
        //1,创建一个工作簿
        Workbook workbook = new HSSFWorkbook();
        //2,创建一个工作表
        Sheet sheet = workbook.createSheet("03统计表");
        //3，创建一行（1，1）
        Row row1 = sheet.createRow(0);
        //4，创建一个单元格
        Cell cell11 = row1.createCell(0);
        cell11.setCellValue("新增观众");
        //（1，2）
        Cell cell12 = row1.createCell(1);
        cell12.setCellValue(666);
        //第二行（2，1）
        Row row2 = sheet.createRow(1);
        Cell cell21 = row2.createCell(0);
        cell21.setCellValue("时间记录");
        //（2，2）
        Cell cell22 = row2.createCell(1);
        cell22.setCellValue(new DateTime().toString("yyyy-MM-dd HH:mm:ss"));
 
        //生成一张表（IO流）03版本使用xls结尾！
        FileOutputStream fileOutputStream = new FileOutputStream(PATH + "03版本测试.xls");
        //输出
        workbook.write(fileOutputStream);
 
        //关闭流
        fileOutputStream.close();
 
        System.out.println("03生成成功");
 
    }
}
```

> 运行结果

![在这里插入图片描述](https://halo-83-start.oss-cn-shenzhen.aliyuncs.com/file/image/typora-202203311246777.png)

> 07和03不同的就这两处：注意对象的一个区别,文件后缀!

```java
//创建对象用XSSF
Workbook workbook = new XSSFWorkbook();
 
//生成一张表（IO流）07版本使用xlsx结尾！
FileOutputStream fos = new FileOutputStream(PATH+"07版本Excel测试.xlsx");
```

`注：大文件写用HSSF`

`缺点：最多只能处理65536行，否则会报异常`

`优点：过程中写入缓存，不操作磁盘，最后一次性写入磁盘，速度快`

```java
java.lang.IllegalArgumentException:Invalid row number (65536) outside allowable range (0.. 65535)
```



```java
    @Test
    public void testwrite03BigData() throws IOException {
        //时间
        long begin = System.currentTimeMillis();
        //创建一个薄
        Workbook workbook = new HSSFWorkbook();
        //创建表
        Sheet sheet = workbook.createSheet();
        //写入数据
        for (int rowNum = 0; rowNum < 65536; rowNum++) {
            Row row = sheet.createRow(rowNum);
            for (int cellNum = 0; cellNum < 10; cellNum++) {
                Cell cell = row.createCell(cellNum);
                cell.setCellValue(cellNum);
            }
        }
        FileOutputStream fos = new FileOutputStream(PATH + "03版本Excel大量数据测试.xls");
        workbook.write(fos);
        fos.close();
        System.out.println("over");
        long end = System.currentTimeMillis();
        System.out.println((double) (end - begin) / 1000);
    }
```
>  运行结果

```
over
3.057
```
> 大文件写用XSSF
> 
> 缺点：写数据时速度非常慢，非常耗内存，也会发生内存溢出，如100万条数据
> 优点：可以写较大的数据量，如20万条数据
```java
Workbook workbook = new XSSFWorkbook();
Fileoutputstream ops = new Fileoutputstream(PATH +"07版本Excel大量数据测试.xlsx");
```

> 大文件写用SXSSF
>
> SXSSWorkbook 来自官方解释：实现：BigGridDemo策略的流式XSSFWorkbook版本。这允许写入非常大的文件而不会耗尽内存，因为任何时候只有可配置的行部分被保存在内存中。
>
> 读取07及以上版本的excel仍然是“XSSFWorkbook”，写入则为“SXSSFWorkbook ”。后缀和07一样是.xlsx。
>
> 最后调用workbook.dispose()删除临时文件。
>
> 优点：可以写非常大量的数据库，如100万条甚至更多条，写数据速度快，占用更少的内存
> 注意：
>
>  过程中会产生临时文件，需要清理临时文件
>  默认由100条记录被保存在内存中，如果超出这数量，则最前面的数据被写入临时文件
>  如果想自定义内存中数据的数量，可以使用new SXSSFWorkbook（数量）

```java
Workbook workbook = new SXSSFWorkbook();
Fileoutputstream ops = new Fileoutputstream(PATH +"07版本Excel大量数据测试.xlsx");
```

```java
    @Test
    public void testwrite07_S_BigData() throws IOException {
        //时间
        long begin = System.currentTimeMillis();
        //创建一个薄
        Workbook workbook = new SXSSFWorkbook(100);
        //创建表
        Sheet sheet = workbook.createSheet();
        //写入数据
        for (int rowNum = 0; rowNum < 65536; rowNum++) {
            Row row = sheet.createRow(rowNum);
            for (int cellNum = 0; cellNum < 10; cellNum++) {
                Cell cell = row.createCell(cellNum);
                cell.setCellValue(cellNum);
            }
        }
        FileOutputStream fos = new FileOutputStream(PATH + "07_S_版本Excel大量数据测试.xlsx");
        workbook.write(fos);
        fos.close();
        //清除临时缓存
        ((SXSSFWorkbook)workbook).dispose();
        System.out.println("over");
        long end = System.currentTimeMillis();
        System.out.println((double) (end - begin) / 1000);
    }
```

## 3.2 POI-Excel读

**03类型**

```java
package com.kuang;
 
import org.apache.poi.hssf.usermodel.HSSFWorkbook;
import org.apache.poi.ss.usermodel.Cell;
import org.apache.poi.ss.usermodel.Row;
import org.apache.poi.ss.usermodel.Sheet;
import org.apache.poi.ss.usermodel.Workbook;
import org.junit.Test;
 
import java.io.FileInputStream;
 
public class ExcelReadTest {
 
    String PATH = "ExcelCreate\\";
 
    @Test
    public void testRead03() throws Exception {
        //获取文件流
        FileInputStream fis = new FileInputStream(PATH + "03版本测试.xls");
        //1、创建一个工作簿。使用 exceL能操作的这边他都可以操作！
        Workbook workbook = new HSSFWorkbook(fis);
        //2、得到表
        Sheet sheet = workbook.getSheetAt(0);
        //3、得到行
        Row row = sheet.getRow(0);
        //4、得到列
        Cell cell = row.getCell(1);
 
        //读取值的时候，一定要注意类型！
        //getStringCellValue 字符串类型
        System.out.println(cell.getNumericCellValue());
        fis.close();
    }
}
```

**07类型**

```java
    @Test
    public void testRead07() throws Exception {
        //获取文件流
        FileInputStream fis = new FileInputStream(PATH + "07版本测试.xlsx");
        //1、创建一个工作簿。使用 exceL能操作的这边他都可以操作！
        Workbook workbook = new XSSFWorkbook(fis);
        //2、得到表
        Sheet sheet = workbook.getSheetAt(0);
        //3、得到行
        Row row = sheet.getRow(0);
        //4、得到列
        Cell cell = row.getCell(0);
 
        //读取值的时候，一定要注意类型！
        //getStringCellValue 字符串类型
        System.out.println(cell.getStringCellValue());
        fis.close();
    }
```

> 注：获取值的类型即可，读取不同的数据类型

```java
    @Test
    public void testCellType() throws Exception {
 
        //获取文件流
        FileInputStream fis = new FileInputStream(PATH +"课题信息表20190701.xlsx");
 
        //创建一个工作簿。使用 excel能操作的这边他都可以操作
        Workbook workbook = new XSSFWorkbook(fis);
        Sheet sheet = workbook.getSheetAt(0);
 
        //获取标题内容
        Row rowTitle = sheet.getRow(0);
        if (rowTitle != null) {
            //得到一行有多少列有数据
            int cellCount = rowTitle.getPhysicalNumberOfCells();
            for (int cellNum = 0; cellNum < cellCount; cellNum++) {
                Cell cell = rowTitle.getCell(cellNum);
                if (cell != null) {
                    int cellType = cell.getCellType();
                    String cellValue = cell.getStringCellValue();
                    System.out.print(cellValue + "|");
                }
            }
            System.out.println();
        }
 
        //获取表中的内容
        //获取表中有多少行有数据
        int rowCount = sheet.getPhysicalNumberOfRows();
        for (int rowNum = 1; rowNum < rowCount; rowNum++) {
            Row rowData = sheet.getRow(rowNum);
            if (rowData != null) {
                //读取列
                int cellCount = rowTitle.getPhysicalNumberOfCells();
                for (int cellNum = 0; cellNum < cellCount; cellNum++) {
                    System.out.println("[" + (rowNum + 1) + "-" + (cellNum + 1) + "]");
 
                    Cell cell = rowData.getCell(cellNum);
                    //匹配列的数据类型
                    if (cell != null) {
                        int cellType = cell.getCellType();
                        String cellValue = "";
 
                        switch (cellType) {
                            case HSSFCell.CELL_TYPE_STRING://字符
                                System.out.print("【 String】");
                                cellValue = cell.getStringCellValue();
                                break;
                            case HSSFCell.CELL_TYPE_BOOLEAN://布尔
                                System.out.print("【 BOOLEAN】");
                                cellValue = String.valueOf(cell.getBooleanCellValue());
                                break;
                            case HSSFCell.CELL_TYPE_BLANK://空
                                System.out.print("【 BLANK】");
                                break;
                            case HSSFCell.CELL_TYPE_NUMERIC://数字(日期、普通数字)
                                System.out.print("【 NUMERIC】");
                                if (HSSFDateUtil.isCellDateFormatted(cell)) {// 日期
                                    System.out.print("--【日期】");
                                    Date date = cell.getDateCellValue();
                                    cellValue = new DateTime(date).toString("yyyy-MM-dd");
                                } else {
                                    //不是日期格式，防止数字过长！
                                    System.out.print("--【转换为字符串输出】");
                                    cell.setCellType(HSSFCell.CELL_TYPE_STRING);
                                    cellValue = cell.toString();
                                }
                                break;
                            case HSSFCell.CELL_TYPE_ERROR://错误
                                System.out.print("【 数据类型错误】");
                                break;
                        }
                        System.out.println(cellValue);
                    }
                }
            }
        }
        //关闭流
        fis.close();
    }
```

**计算公式（了解）**

```java
    @Test
    public void testFormula() throws Exception {
        FileInputStream fis = new FileInputStream(PATH+"公式.xls");
        //创建一个工作簿。使用 excel能操作的这边他都可以操作
        Workbook workbook = new HSSFWorkbook(fis);
        Sheet sheet = workbook.getSheetAt(0);
 
        Row row = sheet.getRow(4);
        Cell cell = row.getCell(0);
 
        //拿到计算公司 evaL
        FormulaEvaluator FormulaEvaluator = new HSSFFormulaEvaluator((HSSFWorkbook) workbook);
 
        //输出单元格的内容
        int cellType = cell.getCellType();
        switch (cellType) {
            case Cell.CELL_TYPE_FORMULA://公式
                String formula = cell.getCellFormula();
                System.out.println(formula);
 
                //计算
                CellValue evaluate = FormulaEvaluator.evaluate(cell);
                String cellValue = evaluate.formatAsString();
                System.out.println(cellValue);
                break;
        }
    }
```

> 运行结果

```sql
SUM(A2:A4)
600.0
```

# 4 EasyExcel操作

## 4.1 EasyExcel写入操作：

- 官方API[ http:// https://www.yuque.com/easyexcel/doc/read](http://xn--https-rfa//www.yuque.com/easyexcel/doc/read)

> 导入依赖

```xml
<!--easyexcel-->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>easyexcel</artifactId>
    <version >2.2.0-beta2</version>
</dependency>
```

由于easyexcel依赖中包含POI相关依赖,有可能发生冲突,所以注释掉

```xml
    <!--导入依赖-->
    <dependencies>
        <!--fastjson-->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
            <version >1.2.62</version>
        </dependency>
 
        <!--easyexcel-->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>easyexcel</artifactId>
            <version >2.2.0-beta2</version>
        </dependency>
    <!--lombok-->
	<dependency>
		<groupId>org.projectlombok</groupId>
		<artifactId>lombok</artifactId>
		<version >1.18.12</version>
	</dependency>
 
<!--        &lt;!&ndash;xLs(03)&ndash;&gt;
        <dependency>
            <groupId>org.apache.poi</groupId>
            <artifactId>poi</artifactId>
            <version>3.9</version>
        </dependency>
        &lt;!&ndash;xLsx(07)&ndash;&gt;
        <dependency>
            <groupId>org.apache.poi</groupId>
            <artifactId>poi-ooxml</artifactId>
            <version>3.9</version>
        </dependency>-->
        <!--日期格式化工具-->
        <dependency>
            <groupId>joda-time</groupId>
            <artifactId>joda-time</artifactId>
            <version>2.10.1</version>
        </dependency>
        <!--test-->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
    </dependencies>
```

> 实体类

```java
package com.kuang.easy;
 
import com.alibaba.excel.annotation.ExcelIgnore;
import com.alibaba.excel.annotation.ExcelProperty;
import lombok.Data;
 
import java.util.Date;
 
@Data
public class DemoData {
    @ExcelProperty("字符串标题")
    private String string;
    @ExcelProperty("日期标题")
    private Date date;
    @ExcelProperty("数字标题")
    private Double doubleData;
    //忽略这个字段
    @ExcelIgnore
    private String ignore;
}
```

> 测试

package com.kuang.easy;

import com.alibaba.excel.EasyExcel;
import org.junit.Test;

import java.util.ArrayList;
import java.util.Date;
import java.util.List;

public class EasyTest {
    String PATH = "ExcelCreate\\";

```java
//模拟写入数据
private List<DemoData> data() {
    java.util.List<DemoData> list = new ArrayList<DemoData>();
    for (int i = 0; i < 10; i++) {
        DemoData data = new DemoData();
        data.setString("字符串" + i);
        data.setDate(new Date());
        data.setDoubleData(0.56);
        list.add(data);
    }
    return list;
}
 
//根据ist写 excel
@Test
public void simplewrite() {
    String fileName = PATH + "EasyTest.xlsx";
    //这里需要指定写用哪个 class去写，然后写到第一个 sheet，名字为模板然后文件流会自动关闭
    //write(fileName,格式类)
    //sheet（表名）
    //doWrite（数据）
    EasyExcel.write(fileName, DemoData.class).sheet("模板").doWrite(data());
}
}
```
## ![在这里插入图片描述](https://halo-83-start.oss-cn-shenzhen.aliyuncs.com/file/image/typora-202203311257422.png)4.2 EasyExcel读取操作：

> 对象

```java
@Data
public class DemoData {
    private String string;
    private Date date;
    private Double doubleData;
}
```

> 监听器

```java
// 有个很重要的点 DemoDataListener 不能被spring管理，要每次读取excel都要new,然后里面用到spring可以构造方法传进去
public class DemoDataListener extends AnalysisEventListener<DemoData> {
    private static final Logger LOGGER = LoggerFactory.getLogger(DemoDataListener.class);
    /**
     * 每隔5条存储数据库，实际使用中可以3000条，然后清理list ，方便内存回收
     */
    private static final int BATCH_COUNT = 5;
    List<DemoData> list = new ArrayList<DemoData>();
    /**
     * 假设这个是一个DAO，当然有业务逻辑这个也可以是一个service。当然如果不用存储这个对象没用。
     */
    private DemoDAO demoDAO;
    public DemoDataListener() {
        // 这里是demo，所以随便new一个。实际使用如果到了spring,请使用下面的有参构造函数
        demoDAO = new DemoDAO();
    }
    /**
     * 如果使用了spring,请使用这个构造方法。每次创建Listener的时候需要把spring管理的类传进来
     *
     * @param demoDAO
     */
    public DemoDataListener(DemoDAO demoDAO) {
        this.demoDAO = demoDAO;
    }
    /**
     * 这个每一条数据解析都会来调用
     *
     * @param data
     *            one row value. Is is same as {@link AnalysisContext#readRowHolder()}
     * @param context
     */
    @Override
    public void invoke(DemoData data, AnalysisContext context) {
        LOGGER.info("解析到一条数据:{}", JSON.toJSONString(data));
        list.add(data);
        // 达到BATCH_COUNT了，需要去存储一次数据库，防止数据几万条数据在内存，容易OOM
        if (list.size() >= BATCH_COUNT) {
            saveData();
            // 存储完成清理 list
            list.clear();
        }
    }
    /**
     * 所有数据解析完成了 都会来调用
     *
     * @param context
     */
    @Override
    public void doAfterAllAnalysed(AnalysisContext context) {
        // 这里也要保存数据，确保最后遗留的数据也存储到数据库
        saveData();
        LOGGER.info("所有数据解析完成！");
    }
    /**
     * 加上存储数据库
     */
    private void saveData() {
        LOGGER.info("{}条数据，开始存储数据库！", list.size());
        demoDAO.save(list);
        LOGGER.info("存储数据库成功！");
    }
}
```

> 持久层

```java
/**
 * 假设这个是你的DAO存储。当然还要这个类让spring管理，当然你不用需要存储，也不需要这个类。
 **/
public class DemoDAO {
    public void save(List<DemoData> list) {
        // 如果是mybatis,尽量别直接调用多次insert,自己写一个mapper里面新增一个方法batchInsert,所有数据一次性插入
    }
}
```

> 测试

```java
/**
 * 最简单的读
 * <p>1. 创建excel对应的实体对象 参照{@link DemoData}
 * <p>2. 由于默认一行行的读取excel，所以需要创建excel一行一行的回调监听器，参照{@link DemoDataListener}
 * <p>3. 直接读即可
 */
@Test
public void simpleRead() {
    // 有个很重要的点 DemoDataListener 不能被spring管理，要每次读取excel都要new,然后里面用到spring可以构造方法传进去
    // 写法1：
    String fileName = TestFileUtil.getPath() + "demo" + File.separator + "demo.xlsx";
    // 这里 需要指定读用哪个class去读，然后读取第一个sheet 文件流会自动关闭
    EasyExcel.read(fileName, DemoData.class, new DemoDataListener()).sheet().doRead();
    // 写法2：
    fileName = TestFileUtil.getPath() + "demo" + File.separator + "demo.xlsx";
    ExcelReader excelReader = null;
    try {
        excelReader = EasyExcel.read(fileName, DemoData.class, new DemoDataListener()).build();
        ReadSheet readSheet = EasyExcel.readSheet(0).build();
        excelReader.read(readSheet);
    } finally {
        if (excelReader != null) {
            // 这里千万别忘记关闭，读的时候会创建临时文件，到时磁盘会崩的
            excelReader.finish();
        }
    }
}
```

# 5 总结

## 5.1 固定套路：

easyexcel使用cv大法，更改的地方主要在重写invoke方法。

1. 写入，固定类格式进行写入
2. 读取，根据监听器设置的规则进行读取！

## 5.2 相关连接

https://www.jianshu.com/p/d1d264c817ef