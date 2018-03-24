SimpleWindow显示最简单的gtk窗口 Note 2018-03-19T14.44.46
========================

### 编译gtk程序
    gcc -o simple simple.c `pkg-config --cflags gtk+-3.0` `pkg-config --libs gtk+-3.0`

### 显示一个空白窗口
#### Makefile

    DIR_SRC = ./src
    DIR_INC = ./include `pkg-config --cflags gtk+-3.0` `pkg-config --libs gtk+-3.0`
    DIR_OBJ = ./obj
    DIR_BIN = ./bin
    
    SRC = $(wildcard ./src/*.c)
    OBJ = $(patsubst %.c,${DIR_OBJ}/%.o, $(notdir ${SRC}))
    
    TARGET = simple
    
    BIN_TARGET = ${DIR_BIN}/${TARGET}
    
    CC = gcc 
    CFLAGS = -g -Wall -I${DIR_INC}
    
    ${BIN_TARGET}:${OBJ}
        $(CC) $(CFLAGS) $(OBJ) -o $@
    
    ${DIR_OBJ}/%.o:${DIR_SRC}/%.c
        $(CC) $(CFLAGS) -c $< -o $@
    
    .PHONY:clean
    clean:
        -rm ${DIR_OBJ}/*.o ${BIN_TARGET}

#### src文件

    #include <gtk/gtk.h>

    static void
    activate (GtkApplication* app,
              gpointer        user_data)
    {
      GtkWidget *window;
    
      window = gtk_application_window_new (app);
      gtk_window_set_title (GTK_WINDOW (window), "Window");
      gtk_window_set_default_size (GTK_WINDOW (window), 200, 200);
      gtk_widget_show_all (window);
    }
    
    int
    main (int    argc,
          char **argv)
    {
      GtkApplication *app;
      int status;
    
      app = gtk_application_new ("org.gtk.example", G_APPLICATION_FLAGS_NONE);
      g_signal_connect (app, "activate", G_CALLBACK (activate), NULL);
      status = g_application_run (G_APPLICATION (app), argc, argv);
      g_object_unref (app);
    
      return status;
    }

### 关键代码
    
    /*设置标题，如果我们不用这个函数的话,
    GTK+将用源文件的名字来作为窗口的标题*/
    gtk_window_set_title(GTK_WINDOW(window), "Center");
    /*设置大小*/
    gtk_window_set_default_size(GTK_WINDOW(window), 230, 150);
    /*设置位置*/
    gtk_window_set_position(GTK_WINDOW(window), GTK_WIN_POS_CENTER);
    /*连接上一个关闭的信号(the destroy signal),
    就是 gtk_main_quit() 这个函数*/
    g_signal_connect_swapped(G_OBJECT(window), "destroy",
            G_CALLBACK(gtk_main_quit), NULL);
