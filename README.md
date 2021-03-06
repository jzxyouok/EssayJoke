## EssayJoke
仿内涵段子

* 模板方法模式构建BaseActivity
* 创建全局的异常捕捉类
* 阿里热修复框架:AndFix
* 自定义热修复框架
* 使用Builder设计模式打造支持链式调用的AlertDialog

            AlertDialog dialog = new AlertDialog.Builder(this)
                    .setContentView(R.layout.detail_common_dialog)
                    .fromBottom(true)
                    .fullWidth()
                    .show();
    
            final EditText et_common = dialog.getView(R.id.comment_editor);
            dialog.setText(R.id.submit_btn, "发送");
            dialog.setOnClickListener(R.id.submit_btn, new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                    Toast.makeText(MainActivity.this, et_common.getText().toString(), Toast.LENGTH_SHORT).show();
                }
            });
            
* 使用Builder设计模式打造通用的支持链式调用的顶部标题栏

        DefaultNavigationBar defaultNavigationBar = new DefaultNavigationBar.Builder(this)
                    .setTitle("投稿")
                    .setRightText("发布")
                    .setOnRightClickListener(new View.OnClickListener() {
                        @Override
                        public void onClick(View v) {
                            Toast.makeText(MainActivity.this, "已经发布", Toast.LENGTH_SHORT).show();
                        }
                    })
                    .setRightIcon(R.mipmap.account_icon_weibo)
                    .build();
                    
* 封装基于OkHttp的网络引擎，链式调用，便于切换引擎

            HttpUtils
                    .with(this)
                    .get()
                    .url("http://is.snssdk.com/2/essay/discovery/v3/")//路径和参数都需要放入到jni中
                    .cache(true)//添加缓存
                    .addParams("iid", "6152551759")
                    .addParams("aid", "7")
                    .excute(new HttpCallBack<DiscoverListResult>() {
    
                        @Override
                        public void onError(Exception e) {
    
                        }
    
                        @Override
                        public void onSuccess(DiscoverListResult result) {
                            // result --> 对象，会添加缓存功能
                            Log.e("请求的最终结果", result.getData().getCategories().getName());
    
                        }
                    });
                    
* 自定义数据库框架(工厂模式+单例模式)并进行优化，数据加密
    * 自定义数据库与网络引擎结合实现数据缓存
    
             IDaoSupport<Person> daoSupport = DaoSupportFactory
                             .getFactory()
                             .getDao(Person.class);
     
             ArrayList<Person> list = new ArrayList<>();
             for (int i = 0; i < 100; i++) {
                 list.add(new Person("sunxin", 1 + i));
             }
             //测试插入数据的效率，并优化
             long startTime = System.currentTimeMillis();
             daoSupport.insert(list);
             long endTime = System.currentTimeMillis();
             
             Log.e("TAG", "Time---》" + (endTime - startTime));
     
            // 查询所有数据的条目数
             List<Person> persons = daoSupport.querySupport().queryAll();
             Log.e(TAG, "initData: "+persons.size() );
     
     
             // 链式调用查询
             List<Person> value = daoSupport
                     .querySupport()
                     .selection("age = ?")
                     .selectionArgs("24")
                     .query();
             Logger.d("------------" + value.size());
             for (Person person : value) {
                 Log.e(TAG, "initData: " + person.toString());
                 Logger.d(person.toString());
             }
                        
* 插件换肤
    * 资源的加载流程分析
        * Resources与AssetManager源码分析
    * Hook拦截View创建分析
        * setContentView源码分析
        * LayoutInflate源码分析
    * 换肤框架的完善
        1. 保证如果换肤，下次进入还要是新皮肤(保存皮肤状态即可)
        2. 重新进入应用需要换肤
        3. 恢复默认皮肤
        4. 自定义View的换肤(提供接口回调)
        
    * 内存泄漏的监测和分析
         1. 垃圾回收问题(GC)
         2. 内存泄漏(不要把Activity的this交出去)
* IPC进程间通信