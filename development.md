### java List转成Tree
``` 
List<KnowledgeTree> newTreeList = knowledgeTreeList.stream().filter(tree -> tree.getParentId()==null).peek(menu -> {
            menu.setChildren(getChildren(menu, knowledgeTreeList));
        }).collect(Collectors.toList());
       
private List<KnowledgeTree> getChildren(KnowledgeTree root, List<KnowledgeTree> all) {
        return all.stream().filter(m1 -> root.getId().equals(m1.getParentId()))
                .peek(m1 -> m1.setChildren(getChildren(m1, all)))
                .collect(Collectors.toList());
    } 
```
## hutools
### 读取json文件
```
String path = "classpath:menuTree.json";
FileReader fileReader = new FileReader(path);
String s = fileReader.readString();
JSONArray jsonArray = JSONUtil.readJSONArray(fileReader.getFile(), Charset.defaultCharset());
List<MenuTree> menuTreeList = JSONUtil.toList(jsonArray, MenuTree.class);
```
### 获取文件路径
```
 public static String getProjectConfPath(String fileName) {
        String filePath = "";
        Properties props = System.getProperties();
        String osName = props.getProperty("os.name");
        if (StringUtils.containsIgnoreCase(osName, "windows")) {
            filePath = System.getProperty("user.dir") + "\\conf" + "\\" + fileName;
        } else {
            filePath = System.getProperty("user.dir") + File.separator + "conf" + "/" + fileName;
        }
        log.info("【----------------------------》" + filePath + "】");
        return filePath;
    }
```
