Makefile编写 Note 2018-03-21T17.00.09
========================



### 函数

#### wildcard

```makefile
$(wildcard 目录/*.c)
#将目录拓展， 结果为  目录/a.c  目录/b.c  目录/c.c
```

#### patsubst

```makefile
$(patsubst %.c, $(OBJ_DIR)/%.o, $(notdir $(src)))
```

