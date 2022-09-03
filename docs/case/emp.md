# 员工管理系统

## RBAC

起因： 为了控制不同人员的不同访问级别，需要进行权限管理，不同级别的人员可以看到与操作的权限不同

1. 建模

   1. 给不同的访问级别的人员分配不同的角色，在 user 表中添加 role 字段

   2. 建立 role 表，将所有的角色统一管理

      - id

      - name

   3. 建立 menu 表，将菜单的路径分开，方便赋予给不同的角色相同的访问权限

      - id
      - name
      - path
      - parentId：便于划分层级结构
      - seq

   4. 因为 role 与 menu 是多对多的关系，避免某个字段出现需要多个 menu 表的行 id,避免之后 java 逻辑中进行再次拆分，需要中间表来进行管理

   5. 因 1.4,建立 role_menu 表，赋予角色所能访问的菜单

      - id

      - role_id

      - menu_id

2. java 建立实体类

   1. user 加 roleId,建 role,建 menu,menu 多加一个 List\<Menu\> children 用于存放当前 menu 下的子节点

3. 从 dao 查出

4. 递归完成层级结构的建立

   menuList 流式操作+递归

   1. filter 出 pid=等于当前节点 id 的元素
   2. collect 起来将这个 list 赋给当前节点的 children
   3. children 重复上面的步骤
   4. 当 parent 为空时终止

5. 将结果写成 json 传入前端

6. 前端获取层级结构递归构建菜单

## 鉴权与授权

1. 鉴权与授权的时机：登录时
