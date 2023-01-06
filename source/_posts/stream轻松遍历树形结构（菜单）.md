---
title: stream轻松遍历树形结构（菜单）
tag: JAVA奇技淫巧
---
### stream轻松遍历树形结构

#### Menu.java
```java
/**
 * Menu
 *
 * @author QingFeng
 * @date 2022/09/13 14:00
 */
    @Data
    @Builder
    public class Menu {
    /**
     * id
     */
     public Integer id;
     /**
     * 名称
     */
     public String name;
     /**
     * 父id ，根节点为0
     */
     public Integer parentId;
     /**
     * 子节点信息
     */
     public List<Menu> childList;


    public Menu(Integer id, String name, Integer parentId) {
        this.id = id;
        this.name = name;
        this.parentId = parentId;
    }
    
    public Menu(Integer id, String name, Integer parentId, List<Menu> childList) {
        this.id = id;
        this.name = name;
        this.parentId = parentId;
        this.childList = childList;
    }

}
```
<!--more-->
#### 递归查询子节点
```java
/**
 * 递归查询子节点
 * @param root  根节点;
 * @param all   所有节点（数据库查询出的所有数据）
 * @return 根节点信息
 */
private List<Menu> getChildrens(Menu root, List<Menu> all) {
    List<Menu> children = all.stream().filter(m -> {
        return Objects.equals(m.getParentId(), root.getId());
    }).map(
            (m) -> {
                m.setChildList(getChildrens(m, all));
                return m;
            }
    ).collect(Collectors.toList());
    return children;
}
```
#### 过滤出父节点为0的节点（根结点）
```java
    List<Menu> menuList = menus.stream().filter(m -> m.getParentId() == 0).map(
            (m) -> {
                m.setChildList(getChildrens(m, menus));
                return m;
            }
    ).collect(Collectors.toList())
```


