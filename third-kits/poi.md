# 学习准备

## POI介绍

POI 是 Apache 软件基金会的开源项目，由 Java 编写的免费开源的跨平台的 Java API，Apache POI 提供 API 给 Java 语言操作 Microsoft Office 的功能。

POI 操作 Excel 文档结构：

- HSSF 提供读写 XLS 格式档案的功能；
- XSSF 提供读写 XLSX 格式档案的功能；

常用API：

- Workbook：Excel 的文档对象，针对不同的 Excel 类型分为：HSSFWorkbook（2003）和XSSFWorkbook（2007）；
- Sheet：Excel 的表单；
- Row：Excel 的行；
- Cell：Excel 的单元格；
- Font：Excel 的字体；
- CellStyle：格子单元样式；

## 导入相关依赖

```xml
<!--poi-->
<dependency>
    <groupId>dom4j</groupId>
    <artifactId>dom4j</artifactId>
    <version>1.6.1</version>
</dependency>
<dependency>
    <groupId>org.apache.poi</groupId>
    <artifactId>poi</artifactId>
    <version>4.0.1</version>
</dependency>
<dependency>
    <groupId>org.apache.poi</groupId>
    <artifactId>poi-ooxml</artifactId>
    <version>4.0.1</version>
</dependency>
<dependency>
    <groupId>org.apache.poi</groupId>
    <artifactId>poi-ooxml-schemas</artifactId>
    <version>4.0.1</version>
</dependency>
<!--poi-->
```

# 简单使用

具体使用步骤都记录在代码注释中；

## 案例1. 创建一个 Excel 文件

```java
@Test
public void createExcel() throws IOException {
    // 1. 创建 Workbook 文档对象
    Workbook workbook = new XSSFWorkbook();
    // 2. 创建表单 Sheet
    workbook.createSheet("sheet1-tassel");
    // 3. 文件输出流，输出文件路径
    FileOutputStream os = new FileOutputStream("C:\\Users\\59498\\Desktop\\PoiPr\\test1.xlsx");
    // 4. 输出到文件
    workbook.write(os);
    // 5. 关闭流，释放空间
    os.close();
}
```

## 案例2. 创建单元格

```java
@Test
public void createCell() throws IOException {
    // 1. 创建 Workbook 文档对象
    Workbook workbook = new XSSFWorkbook();
    // 2. 创建表单 Sheet
    Sheet sheet = workbook.createSheet("sheet1");
    // 3. 创建行对象，下标从 0 开始，代表哪一行
    Row row = sheet.createRow(2);
    // 4. 创建单元格，下标从 0 开始，代表这一行的哪一列的单元格
    Cell cell = row.createCell(1);
    // 5. 给单元格设置值
    cell.setCellValue("Ep流苏");
    // 6. 文件输出流，输出文件路径
    FileOutputStream os = new FileOutputStream("C:\\Users\\59498\\Desktop\\PoiPr\\createCell.xlsx");
    // 7. 输出到文件
    workbook.write(os);
    // 8. 关闭流，释放空间
    os.close();
}
```

## 案例3. 给单元格设置样式

```java
@Test
public void createCellWithStyle() throws IOException {
    Workbook workbook = new XSSFWorkbook();
    Sheet sheet = workbook.createSheet("sheet1");
    Row row = sheet.createRow(2);
    Cell cell = row.createCell(1);
    cell.setCellValue("Ep流苏");

    CellStyle cellStyle = workbook.createCellStyle();

    // 设置边框
    cellStyle.setBorderBottom(BorderStyle.DOUBLE);
    cellStyle.setBorderTop(BorderStyle.DOTTED);
    cellStyle.setBorderLeft(BorderStyle.HAIR);
    cellStyle.setBorderRight(BorderStyle.MEDIUM);

    // 设置字体
    Font font = workbook.createFont();
    font.setFontName("微软雅黑");
    font.setFontHeightInPoints((short) 32);
    cellStyle.setFont(font);

    // 跨行跨列 合并单元格
    CellRangeAddress region = new CellRangeAddress(2, 5, 1, 3);
    sheet.addMergedRegion(region);
    // 水平居中
    cellStyle.setAlignment(HorizontalAlignment.CENTER);
    // 垂直居中
    cellStyle.setVerticalAlignment(VerticalAlignment.CENTER);
    cell.setCellStyle(cellStyle);
    FileOutputStream os = new FileOutputStream("C:\\Users\\59498\\Desktop\\PoiPr\\createCellWithStyle.xlsx");
    workbook.write(os);
    os.close();
}
```

## 案例4. 读取内容

**工具类** : 根据数据类型返回对应类型的值；

ExcelUtils.java

```java
public class ExcelUtils {
    /**
     * 处理 Excel 数据类型
     */
    public static Object getValue(Cell cell) {
        Object value = null;
        switch (cell.getCellType()) {
            case STRING:
                value = cell.getStringCellValue();
                break;
            case BOOLEAN:
                value = cell.getBooleanCellValue();
                break;
            // 数字类型（包含日期和普通数字）
            case NUMERIC:
                if (DateUtil.isCellDateFormatted(cell)) {
                    value = cell.getDateCellValue();
                } else {
                    value = cell.getNumericCellValue();
                }
                break;
            case FORMULA:
                value = cell.getCellFormula();
                break;
            default:
                break;
        }
        return value;
    }
}
```

```java
@Test
public void readData() throws IOException {
    Workbook workbook = new XSSFWorkbook("C:\\Users\\59498\\Desktop\\PoiPr\\项目鉴定信息一览表.xlsx");
    Sheet sheet = workbook.getSheetAt(0);
    // 得到最后一行行号
    int lastRowNum = sheet.getLastRowNum();
    System.out.println(lastRowNum);
    for (int i = 0; i <= lastRowNum; i++) {
        // 得到第i行
        Row row = sheet.getRow(i);
        short lastCellNum = row.getLastCellNum();
        System.out.println(lastCellNum);
        StringBuilder sb = new StringBuilder();
        for (int j = 0; j < lastCellNum; j++) {
            // 得到第j个单元格
            Cell cell = row.getCell(j);
            // 根据类型得到值
            Object value = ExcelUtils.getValue(cell);
            sb.append(value + " ");
        }
        System.out.println(sb.toString());
    }
}
```

## 案例5. 导入

保存数据的实体类：

Project.java

```java
@Data
public class Project {
    private Integer id;
    private String name;
    private String firstAuthorType;
    private String firstAuthor;
    private String dept;
    private List<String> authors;

    public Project(){}
    public Project(Object[] objects) {
        this.id = Math.round(10000000);
        this.name = objects[0].toString();
        this.firstAuthorType = (String) objects[1];
        this.firstAuthor = objects[2].toString();
        this.dept = objects[3].toString();
        String aus = objects[4].toString();
        String[] strings = aus.split(",");
        this.authors = Arrays.asList(strings);
    }
}
```

```java
@Test
public void upload() throws IOException, InvalidFormatException {
    File file = new File("C:\\Users\\59498\\Desktop\\PoiPr\\项目鉴定信息一览表.xlsx");
    Workbook sheets = new XSSFWorkbook(file);
    Sheet sheet = sheets.getSheetAt(0);
    int lastRowNum = sheet.getLastRowNum();
    List<Project> projects = new ArrayList<>();
    for (int i = 1; i <= lastRowNum ; i++) {
        Row row = sheet.getRow(i);
        short lastCellNum = row.getLastCellNum();
        Object [] objs = new Object[lastCellNum];
        for (int j = 0; j < lastCellNum; j++) {
            Cell cell = row.getCell(j);
            Object value = ExcelUtils.getValue(cell);
            objs[j] = value;
        }
        Project project = new Project(objs);
        projects.add(project);
    }
    projects.forEach(project -> {
        System.out.println(project);
    });
}
```

## 案例6. 导出

这里使用Map 模拟数据库表记录；

实体类带数据

Project.java

```java
@Data
public class Project {
    private Integer id;
    private String name;
    private String firstAuthorType;
    private String firstAuthor;
    private String dept;
    private List<String> authors;

    /**
     * 模拟数据库中的数据
     */
    public static List<Project> getProjects() {
        List<Project> projects = new ArrayList<>();
        for (int i = 1; i < 6; i++) {
            projects.add(new Project(i, "项目" + i, "type" + i, "author" + i, "dept" + i, Arrays.asList("author1,author2,author3")));
        }
        return projects;
    }

    public Project(){}

    public Project(Integer id, String name, String firstAuthorType, String firstAuthor, String dept, List<String> authors) {
        this.id = id;
        this.name = name;
        this.firstAuthorType = firstAuthorType;
        this.firstAuthor = firstAuthor;
        this.dept = dept;
        this.authors = authors;
    }
}
```

```java
@Test
public void download() throws IOException {
    List<Project> projects = Project.getProjects();
    Workbook workbook = new XSSFWorkbook();
    Sheet sheet = workbook.createSheet("project");
    // 设置表头
    String title[] = {"项目名称", "第一完成人类型", "第一完成人", "部门", "项目成员"};
    Row row = sheet.createRow(0);
    for (int i = 0; i < title.length; i++) {
        Cell cell = row.createCell(i);
        cell.setCellValue(title[i]);
    }
    //设置内容
    for (int i = 0; i < projects.size(); i++) {
        row = sheet.createRow(i + 1);
        Project project = projects.get(i);
        Cell nameCell = row.createCell(0);
        nameCell.setCellValue(project.getName());
        Cell typeCell = row.createCell(1);
        typeCell.setCellValue(project.getFirstAuthorType());
        Cell authorCell = row.createCell(2);
        authorCell.setCellValue(project.getFirstAuthor());
        Cell deptCell = row.createCell(3);
        deptCell.setCellValue(project.getDept());
        Cell authorsCell = row.createCell(4);
        authorsCell.setCellValue(project.getFirstAuthor() + "," + project.getAuthors().toString().replace("[", "").replace("]", ""));
    }

    // //spring mvc 使用 参数 (HttpServletResponse response)
    // String fileName = URLEncoder.encode("项目表.xlsx", "UTF-8");
    // response.setContentType("application/octet-stream");
    // response.setHeader("content-disposition", "attachment;filename" + fileName);
    // response.setHeader("filename", fileName);
    // workbook.write(response.getOutputStream());
    FileOutputStream os = new FileOutputStream("C:\\Users\\59498\\Desktop\\PoiPr\\项目表.xlsx");
    workbook.write(os);
}
```

