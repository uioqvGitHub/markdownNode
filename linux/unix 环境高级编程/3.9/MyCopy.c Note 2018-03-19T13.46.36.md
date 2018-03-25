[TOC]

MyCopy.c 3.9
========================

### src/MyCopy.c

```c
#include "apue.h"

#define BUFFSIZE 4096

int main(void) {
    int n;
    char buf[BUFFSIZE];


    while((n = read(STDIN_FILENO, buf, BUFFSIZE)) > 0)
        if(write(STDOUT_FILENO, buf, n) != n)  
            err_sys("write error");
    if(n < 0)  
        err_sys("read error");

    exit(0);
    
}
```

### Makefile

```makefile
.PHONY: clean all 

DIR_ROOT = $(dir $(abspath $(lastword $(MAKEFILE_LIST))))
ifndef DIR_OBJ
DIR_OBJ = $(DIR_ROOT)/obj
endif
DIR_SRC = $(DIR_ROOT)/src
DIR_INC = $(DIR_ROOT)/include
DIR_BIN = $(DIR_ROOT)/bin
DIR_LIB = $(DIR_ROOT)/lib

OBJ1_ROOT = ../utils


DIR_SRC += $(OBJ1_ROOT)/src
DIR_INC += $(OBJ1_ROOT)/include

TARGET = mycopy

SRC = $(wildcard $(addsuffix /*.c, $(DIR_SRC)))
OBJ = $(patsubst %.c, $(DIR_OBJ)/%.o, $(notdir $(SRC)))
BIN = $(DIR_BIN)/$(TARGET)
LIB = $(DIR_LIB)/$(TARGET).a

CC = cc
CFLAGS = -g -Wall $(addprefix -I,$(DIR_INC))
AR = ar
ARFLAGS = rcs
$(BIN):$(LIB) 
    $(CC) $< -o $@
$(LIB):$(OBJ)
    $(AR) $(ARFLAGS) $@ $^
$(DIR_OBJ)/%.o:$(word 1, $(DIR_SRC))/%.c
    $(CC) $(CFLAGS) -c $< -o $@
$(DIR_OBJ)/%.o:$(word 2, $(DIR_SRC))/%.c
    $(CC) $(CFLAGS) -c $< -o $@

clean: 
    -rm $(OBJ)
```

