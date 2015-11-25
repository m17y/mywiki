
---
title: "GTK Simple"
date: 2015-11-05 11:16
---



GTK入门
==============================

####gtk常用函数：
-------------
	gtk_dialog_new 新建窗口构件
	gtk_window_set_title 设置window->title
	gtk_table_attach 向格状容器的指定区域中添加控件
	gtk_container_set_border_width 设置窗口宽度
	gtk_box_pack_start将构件放在顶部或左边
	gtk_dialog_add_buttons 添加button
	g_signal_connect(instance, detailed_signal, c_handler, data) 绑定signal事件及触发函数
*GtkEntry*

	gtk_entry_new 创建行编辑
	gtk_entry_get_text (GtkEntry *entry) 获得文本内容
	gtk_entry_set_text (GtkEntry *entry, const gchar *text) 设置行编辑的内容
	gtk_editable_set_editable (GtkEditable *editable, gboolean is_editable) 设置编辑模式
	gtk_entry_set_visibility (GtkEntry *entry, gboolean visible) 显示模式(密码模式)

*GtkCheckButton*

	gtk_check_button_new_with_label (const gchar *label) 创建带文本内容的复选框
	
*GtkComboBox*
		
	gtk_combo_box_new_text (void) 创建下拉框
	gtk_combo_box_append_text (GtkComboBox *combo_box, const gchar *text) 追加下拉框
	gtk_combo_box_get_active_text (GtkComboBox *combo_box) 获得当前下拉框的内容     

*GtkTreeView*

	gtk_list_store_new (2, G_TYPE_STRING, G_TYPE_BOOLEAN); 创建model
	gtk_tree_model_get_n_columns(pmodel) 获取行数
	gtk_tree_view_get_model(GTK_TREE_VIEW1)) 得到TREEVIEW的model
	gtk_tree_model_get_iter (model_iter, &iter, path) 获取列表指定位置的值（DISKS_COL_DEVICE）


####gtk 简单组建：
-------------
*Combo example*

	gtk_init (&argc, &argv); 初始化
	target_tbl = gtk_table_new (3, 3, FALSE); 创建容器
	GtkWidget *device_name = gtk_label_new (_(value)); label
	GtkWidget *combo = gtk_combo_box_text_new();
	    gtk_combo_box_text_append_text(GTK_COMBO_BOX_TEXT(combo),
                                   "For Test One");
	    gtk_combo_box_text_append_text(GTK_COMBO_BOX_TEXT(combo),
                                   "For Test two");
	gtk_table_attach_defaults(GTK_TABLE(table),device_name,0,1,0,1); 布局＆将组建塞入窗口
	gtk_table_attach_defaults(GTK_TABLE(table),combo,1,2,0,1);
	gtk_widget_show_all(dialog); show 窗口
	
**important**
	*SIGNAL*
	
	g_signal_connect (G_OBJECT (combo), "changed",G_CALLBACK (group_change),NULL);
	
	G_OBJECT (combo) 绑定combo组建
	"changed"监听组建事件，不同的组建有不同的事件，最好去查下API
	G_CALLBACK (group_change) 绑定回调函数 changed时激发函数内事件
		
**g_signal_connect(instance, detailed_signal, c_handler, data)**
	

    Api 解释：
    		Connects a GCallback function to a signal for a particular object.The handler will be called before the default handler of the signal.
    		instance :the instance to connect to.
    		detailed_signal :a string of the form "signal-name::detail".
    		c_handler :the GCallback to connect.
    		data :data to pass to c_handler calls.
    		Returns :the handler id
**TreeView**

>在GTK+中, 使用GtkTreeModel接口和GtkTreeView构件, 可以创建树形或列表控件. 此构件采用了Model/View/Controller设计, 包含4个主要部分:
*Model/View/Controller设计*：
(1) Tree view构件(GtkTreeView)
(2) 视图列(GtkTreeViewColumn)
(3) 单元格[cell]呈现器(GtkCellRenderer等)
(4) 模型接口(GtkTreeModel)

*创建：*
	
	gtk_list_store_new (2, G_TYPE_STRING, G_TYPE_BOOLEAN); 创建model
	gtk_tree_store_append (store, &iter, NULL) 取得迭代器
	gtk_tree_store_new (disks_store, &iter,  
                 DISKS_COL_DEVICE, root_disk,
                 DISKS_COL_SIZE, size_gb,
                 -1) 数据添加到model | Iter, 此迭代器指向数据将要被添加的位置
    disks_col_convert = gtk_cell_renderer_toggle_new ();
    gtk_tree_view_insert_column_with_attributes (
	 disks_list, //表头文字  
	 0, //列标
	 _("Convert"),
         disks_col_convert, //cell renderer 
         "active", DISKS_COL_CONVERT, //多个attributes 
         NULL);
	
*回调函数里取得(写入)选择行的数据：*
	
	gtk_tree_selection_get_selected(GTK_TREE_SELECTION(widget), &model, &iter)
	／／获取 path
	path = gtk_tree_path_new_from_string (path_str);
	//根据path获得iter
        gtk_tree_model_get_iter (model_iter, &iter, path);
	／／获取列表指定位置的值（DISKS_COL_DEVICE）
	gtk_tree_model_get(model, &iter, DISKS_COL_DEVICE, &value, 0);
	／／写入值->指定位置
	gtk_list_store_set (GTK_LIST_STORE (model), &iter,
                      DISKS_COL_MODEL, "For Test", 0);
	
 *API*
 
> void                gtk_tree_store_set                  (GtkTreeStore *tree_store,  GtkTreeIter *iter, ...);
> 
> Sets the value of one or more cells in the row referenced by iter. The variable argument list should contain integer column numbers, each column number followed by the value to be set. The list is terminated by a -1. For example, to set column 0 with type G_TYPE_STRING to "Foo", you would write gtk_tree_store_set (store, iter, 0, "Foo", -1).

> The value will be referenced by the store if it is a G_TYPE_OBJECT, and it will be copied if it is a G_TYPE_STRING or G_TYPE_BOXED.

> tree_store :A GtkTreeStore
> iter :A valid GtkTreeIter for the row being modified
> ... :
> pairs of column number and value, terminated with -1


###推荐API 工具
	devhelp 需要在linux下安装软件 ，方便查看
	(下载glade界面设计(GTK)之后ubuntu中可以搜索到)
	
*参考链接*


[*Gnome 官网api*](https://developer.gnome.org/references)

[*CSDN BLOG ---> treeView*](http://blog.csdn.net/crazyingbird/article/details/653119)

[*CSDN BLOG ---> gtk Box Model*](http://blog.csdn.net/liu1164316159/article/details/17678365)

[*遍历treeView*](http://www.cppblog.com/dfghj44444/archive/2010/06/25/118699.html)

[TreeView Double Click Event](http://blog.chinaunix.net/xmlrpc.php?r=blog/article&uid=25793640&id=3047846)


