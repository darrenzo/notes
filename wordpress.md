# wordpress

- 文件要用utf-8

## 几个方法

- bloginfo('name');      bloginfo函数支持传一个参数，用于获取数据库中options表对应的数据并直接输出

- echo get_bloginfo('name');    get_bloginfo('name')可以返回一个数据用变量接收，这里用echo输出

- echo get_option('name','jack'); 从数据库option中获取name的数据，如果找不到name，默认输出jack，第二个参数jack也可以不写

- update_option('view',3)  去数据库option中查找view，并把3赋值给view，如果找不到view就创建后再赋值

```php
    <?php
        $view = get_option('view');
        update_option('view', $view+1);
    ?>
    <p>访问量：<?php echo $view; ?></p>
```

- have_posts() 判断是否有文章，返回并输出Boolean

- the_post() 获取下一篇文章的信息并输出，并且将信息存入全局变量 $post 中

- the_title() 获取当前日志的标题并输出，从$post中的属性找

- the_permalink() 获取当前日志的链接并输出，从$post中的属性找

- the_content()  获取当前日志的内容并输出，从$post中的属性找

## 元数据

- the_category(",")  获取当前日志的分类名并输出，如果日志同时属于多个分类，则都显示出来，用“，”分隔，不传参数就按 “，”

- the_author() 获取当前日志的作者名并输出

- the_time("Y-m-d H:i:s")  获取当前日志的发布时间并输出,不传参数就按wp后台设置中的默认形式

- `edit_post_link('edit','<span>|</span>','')`  创建一个链接到wp后台写文章界面编辑文章，第一个参数是链接显示的名字，第二个是链接左边的内容，第三个参数是链接右边的内容

## 多语言

- _e("argus","zww")  在当前主题文件下查找zww语言项目名（是Poedit创建时的项目名，不是文件名），并查找argus的对应翻译再输出，如果找不到，就输出原文

- __() 和以上方法一样，只是不会自动输出

- 新建functions.php，添加启动翻译action

## 导航栏链接和侧边栏

- posts_nav_link()   自动创建翻页链接“上一页”-“下一页”

- 新建sidebar.php文件 编译侧边栏

- get_sidebar()  加载sidebar.php模板文件

- 写在functions.php中，用于注册一个小工具，可以在wp后台外观子项中找到

```php
    <?php
        register_sidebar(
            array(
                'name' => '侧边栏',
                'before_widget' => '<div class="sbox">',
                'after_widget'  => '</div>',
                'before_title'  => '<h2>',
                'after_title'   => '</h2>'
            )
        );
    ?>
```

- dynamic_sidebar('侧边栏')  调用侧边栏小工具，写在sidebar.php中,支持传递参数，为侧边栏name值，不传参数默认调用第一个，调用一次此方法只显示一个侧边栏

- is_dynamic_sidebar()   判断是否存在侧边栏小工具

- wp_list_cats()  获取分类链接列表

- wp_list_pages()  获取页面列表

- get_links()  获取友情链接

- wp_register()  获取注册链接（登录成功时显示“管理站点”）

- wp_loginout()  获取退出链接（未登录时显示“登录”）

## 头部底部

- get_header()  导入header.php文件

- get_footer()  导入footer.php文件

## 分类模板

- single_cat_title()  获取当前分类的标题，写在category.php中

- get_cat_ID(''); 获取当前分类名称的ID

- category-id.php id为各分类的id值，优先显示此类模板,id可在数据库terms表查看

- category-分类别名.php  优先显示此类模板

- category.php 为分类调用的默认模板，找不到其他两种模板时显示

- $terms = wp_get_post_terms( $post_id, $taxonomy, $args ); 返回$args范围下值为$taxonomy的数组，Returns Array of Term ID's for "my_taxonomy"

```php
  <?php $term_list = wp_get_post_terms($post->ID, 'my_taxonomy', array("fields" => "ids")); ?>
```

## 文章页模板

- single.php

- previous_post_link("上一篇：%link") 上一篇文章链接 %link显示为文章名，不传参数默认显示 箭头 + %link

- next_post_link("下一篇：%link") 下一篇文章链接

## 文章页自定义栏目

- add_post_meta($post->ID,'download','www.11/1.rar',true) 添加文章自定义栏目，第二个参数为栏目名，如果前缀加_，则为隐藏的栏目，第三个参数为download的值，第四个参数为download是否唯一，唯一则不能添加；也可以在wp后台文章选项直接添加

- get_post_meta($post->ID,'download',true) 返回文章自定义栏目，用一个变量接收，第一个参数为当前文章id（自动获取），第二个为自定义栏目名，第三个参数true表示返回字符串，一个自定义栏目可以有多个值，但是只显示第一个，false表示返回数组，多个值则需要另外foreach遍历输出

- update_post_meta($post->ID,'download','www.hs/5.rar','www.hs/1.rar') 更新文章自定义栏目,第三个参数为给download设置的新值，第四个参数为，给原值为'www.hs/1.rar'的'download'项批量替换为'www.hs/5.rar'，也可以不传。

- delete_post_meta($post->ID,'download','www.hs/1.rar') 删除文章自定义栏目，删除值为'www.hs/1.rar'的download的项，不传第三个参数，直接删除所有的download的项

## 不同的分类调用不同的文章模板

- get_template_part($slug,$name) 调用自定义模板$slug-sname.php文件，如果不存在，则调用$slug.php文件

- get_the_ID() 获取当前文章ID,等价于$post->ID

- `get_the_category($post->ID)` 获取当前文章分类,返回一个数组，用变量接收，比如`$cat,$cat[0]->slug`得到的是当前分类的别名   name是当前分类名

- get_category_link($cat[0]) 获取当前分类链接

## 独立页面模板调用

- page.php 不存在则默认调用index.php

- page-ID.php 不存在则调用page.php

- page-独立页面别名.php  不存在则调用page.php

## 评论模板调用

- comments.php

- comments_template() 调用评论模板

- comments_open() 判断当前文章是否开启评论功能，返回Boolean

- post_password_required() 判断当前文章是否需要密码且正确输入，如果需要密码且正确输入，返回false,没有正确输入，返回true

- have_comments() 判断当前文章是否有评论

- wp_list_comments() 输出当前文章所有评论

- comment_registration

- template_directory 当前模板所在的目录

- url 主页的链接

- name 主页的名字

- is_user_logged_in() 判断用户是否登录

- comment_form() 输出评论窗口

## google文件加载慢的应对插件

- gravatar-fixed 解决头像无法显示问题

- googlefontsto360 解决字体加载慢问题

## 搜索页模板

- search.php

- get_search_query() 返回当前搜索词，用变量接收

## 404页面模板调用

- 404.php

## 菜单调用

- wp_nav_menu(array('menu'=>'zww')) 调用菜单模板，放在header.php底部,如果有参数，则显示指定的菜单

## 模板判断函数

- is_home()  判断当前是否在首页模板

- is_category() 判断当前是否在分类模板

- is_search() 判断当前是否在搜索模板

- is_404() 判断当前是否在404模板

- is_single() 判断当前是否在文章页模板

- is_page() 判断当前是否在独立页面模板

- 以上判断可以实现按需加载和面包屑导航

- `wp_title($sep,$echo,$seplocation)` 自动识别模板，返回对应页面的标题，但是在首页，什么也不显示,`$sep`表示分隔符，`$echo`表示直接显示还是只是返回值，$seplocation表示sep位置，right为右，不传值默认左

- $paged 全局变量，返回当前分页是第几页，用变量接收，第一页为0，第二页为2

## 面包屑导航

- $s  返回搜索词，用变量接收

## 自定义函数

- 调用functions.php中的函数wz()

```php
    <?php wz(); ?>
```

## get_posts($args)

- 调用多篇文章，返回的是数组型的变量，用一个变量接收

```php
    <?php
    $args = array(
        'numberposts'     => 5, // 需要提取的文章数
        'offset'          => 0, // 以第几篇文章为起始位置
        'category'        => '1', // 分类的ID，多个用逗号将分类编号隔开
        'orderby'         => 'post_date', // 排序规则
        'order'           => 'DESC', // 'ASC' —— 升序 （低到高）'DESC' —— 降序 （高到底）
        'include'         => , // 要显示文章的ID
        'exclude'         => , // 要排除文章的ID
        'meta_key'        => , // 自定义字段名称
        'meta_value'      => , // 自定义字段的值，配合上一个参数，来选择显示符合自定义字段数值的文章
        'post_type'       => 'post', // post（日志）——默认，page（页面），attachment（附件），any —— （所有）
        'post_mime_type'  => , // 文章的 mime 类型
        'post_parent'     => , // 要显示文章的父级 ID
        'post_status'     => 'publish', // 文章状态
        'tax_query' => [   // 处理多个分类
                                [
                                    'taxonomy' => 'knowledgebase_category', // 分类名称
                                    'field' => , // 'slug'或'id'
                                    'terms' => 'battery-powered-camera'  // 跟上面成对出现，’field’ => ‘slug’，则’terms’ => array( ‘action’, ‘commedy’ ); ’field’ => ‘id’,，则’terms’ => array( 103, 115, 206 )
                                ]
                           ] );
    $posts_array = get_posts( $args );
    ?>
```

- orderby排序规则：
- ‘author’ —— 按作者数值编号排序
- ‘category’ —— 按类别数值编号排序
- ‘content’ —— 按内容排序
- ‘date’ —— 按创建日期排序
- ‘ID’ —— 按文章编号排序
- ‘menu_order’ —— 按菜单顺序排序。仅页面可用。
- ‘mime_type’ —— 按MIME类型排序。仅附件可用。
- ‘modified’ —— 按最后修改时间排序。
- ‘name’ —— 按存根排序。
- ‘parent’ —— 按父级ID排序
- ‘password’ —— 按密码排序
- ‘rand’ —— 任意排序结果
- ‘status’ —— 按状态排序
- ‘title’ —— 按标题排序
- ‘type’ —— 按类型排序

## setup_postdata()

## wp_db类

- global $wpdb 使用方法前，要先声明$wpdb

- $wpdb->show_errors(); 显示错误，要放在执行语句前面，SQL语句写错默认是不报错的，执行此语句后可以报错

- $wpdb->print_error(); 必须要show_errors()存在才生效，只打印最后一次的报错

- $wpdb->hide_errors(); 此语句之后的错误均不显示

- echo $wpdb->last_error;  显示最新的错误，没有位置要求

- $wpdb->insert($table,array('xm' => 'zww','xb' => 'man', 'xq' => 'wp'),$dateformat); 向数据库中添加一条数据，$table为表名，第二个参数传一个数组，为要添加的具体数据，第三个为数据的格式，可以不传

- $wpdb->insert_id 返回自动增长的当前ID，用变量接收

- $wpdb->prefix  返回数据表的前缀，用变量接收,一般用于$wpdb->prefix.'demo'形式

- $wpdb->update( $wpdb->prefix.'demo',array('xm' => 'xh','xb' => 'girl'),array('id' => 1) );  更新数据库表的数据，第一个参数为表名，第二个参数为更改后的信息，第三个参数为要更改的地方

- $wpdb->get_var('SELECT xm FROM `'. $wpdb->prefix .'demo`WHERE`id` = 6' );在demo表中id为6的行取xm列的数据，注意变量名用``反引号引起来，表中行列id都是从0开始，返回字符串，用变量接收
- `$wpdb->get_var('SELECT * FROM wp_demo', 3 , 2 );` 在demo表中 * 表示取第一行第一列的数据，3表示列的id，为第四行，2位行的id，为第三行,不用双引号

- $wpdb->get_var('SELECT ROW_COUNT()');  返回上一个SQL操作影响的行数，用变量接收

- $wpdb->query('SQL语句'); 执行SQL语句  $wpdb->query('DELETE FROM `'.$wpdb->prefix.'demo` WHERE `id` = 7'); 删除ID为7的一行

- $rows = $wpdb->get_results('SELECT * FROM `' . $wpdb->prefix . 'demo`',ARRAY_A); 返回数据库demo表的所有数据，不传第二个参数就默认返回一个OBJECT，用变量接收，如$rows，遍历时echo $rows->$row输出，如果设置ARRAY_A，则返回一个数组，遍历时echo $rows['$row']输出,另外还有OBJECT_K和ARRAY_N类型

- $rows = $wpdb->get_row('SELECT * FROM `' . $wpdb->prefix . 'demo`',ARRAY_A,1);  只返回数据库demo表中一行的数据，设置的ARRAY_A表示返回的是数组，1表示第二行

## add_action  add_filter
