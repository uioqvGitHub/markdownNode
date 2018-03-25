## PrintFL.c
### src/PrintFL.c

```c
#include "apue.h"
#include <fcntl.h>

/*
 * 打印指定文件FL   
 * */
int main(int argc, char* argv[]) {
    int val;

    if(argc != 2)
        err_quit("usage: a.out <descriptor#>");

    if((val = fcntl(atoi(argv[1]), F_GETFL, 0)) < 0)
        err_sys("fcntl error for fd %d", atoi(argv[1]));

    switch( val & O_ACCMODE ){
    case O_RDONLY:
        printf("read only");
        break;
    
    case O_WRONLY:
        printf("write only");
        break;
    case O_RDWR:
        printf("read write");
        break;
    default:
        err_dump("unknown access mode");
    }   

    if( val & O_APPEND )
        printf(", append");
    if( val & O_NONBLOCK )
        printf(", nonblocking");
    if( val & O_SYNC )
        printf(", synchronous writes");

#if !defined(_POSI_C_SOURCE) && defined(O_FSYNC) && (O_FSYNC != O_SYNC)
    if (val & O_FSYNC)
        printf(", synchronous writes");
#endif

    putchar('\n');
    exit(0);
}
```

### src/SetFL.c

```c
#include "apue.h"
#include <fcntl.h>

void
set_fl(int fd, int flags) {
    int val;

    if ((val = fcntl(fd, F_GETFL, 0)) < 0)
        err_sys( "fcntl F_GETFL error" );

    val |= flags;

    if( fcntl( fd, F_SETFL, val ) < 0 ) 
        err_sys("fcntl F_SETFL error");
}
```



###Makefile

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

TARGET = printFL

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
    -rm $(OBJ) $(BIN) $(LIB)
```