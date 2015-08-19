# EasyRecyclerView
这个库使用了部分[Malinskiy/SuperRecyclerView](https://github.com/Malinskiy/SuperRecyclerView)的代码，并参考[wanglg/SuperRecyclerView](https://github.com/wanglg/SuperRecyclerView)。  
去掉了不常用的Swipe包。将加载更多交给了adapter实现。  
重点在Adapter与viewholder的封装。他们之间彻底解耦。adapter工作更少，viewholder将可以到处复用。并不会影响效率。  
支持数据管理，Header与Footer添加，加载更多。没有更多。加载错误  


##依赖
`compile 'com.jude:easyrecyclerview:2.0.1'`


##示例
![recycler.gif](recycler3.gif)
##EasyRecyclerView的使用

        <com.jude.easyrecyclerview.EasyRecyclerView
          android:id="@+id/recyclerView"
          android:layout_width="match_parent"
          android:layout_height="match_parent"
          app:layout_empty="@layout/view_empty"
          app:layout_progress="@layout/view_progress"
          />

平常这样就好。layout_empty 与 layout_progress 需自行指定，若不指定将不会出现。并且只能在xml被指定。  

属性列表

          <attr name="layout_empty" format="reference" />
          <attr name="layout_progress" format="reference" />
          <attr name="recyclerClipToPadding" format="boolean" />
          <attr name="recyclerPadding" format="dimension" />
          <attr name="recyclerPaddingTop" format="dimension" />
          <attr name="recyclerPaddingBottom" format="dimension" />
          <attr name="recyclerPaddingLeft" format="dimension" />
          <attr name="recyclerPaddingRight" format="dimension" />
          <attr name="scrollbarStyle">
              <flag name="insideOverlay" value="0x0" />
              <flag name="insideInset" value="0x01000000" />
              <flag name="outsideOverlay" value="0x02000000" />
              <flag name="outsideInset" value="0x03000000" />
          </attr>

注意EasyRecyclerView本质并不是一个RecyclerView

**设置空白View与加载View**
xml中  
`app:layout_empty="@layout/view_empty"`  
`app:layout_progress="@layout/view_progress"`  
代码中  
`void setEmptyView(View emptyView)`  
`void setProgressView(View progressView)`  
然后可以随时显示他们  
`void showEmpty()`  
`void showProgress()`  
`void showRecycler() `  

##RecyclerArrayAdapter<T>  
**整合了数据增删的功能**  
`void add(T object) `  
`void addAll(Collection<? extends T> collection)`  
`void addAll(T ... items) `  
`void insert(T object, int index)`  
`void remove(T object)`  
`void clear()`  


**整合的Header与Footer的实现**  
`void addHeader(ItemView view)`  
`void addFooter(ItemView view)`  
ItemView不是view而是view生成器  
对应Adapter的onCreate与onBind方法,所以onCreate后会多次onBind。  
建议数据加载完毕后再add。onCreate里初始化UI。不使用onBind。  
添加过后需手动`notifyDataSetChanged();`  

        public interface ItemView {
             View onCreateView(ViewGroup parent);
             void onBindView(View itemView);
        }


###下面的功能在adapter最后添加一个footer。来显示各种状态。

**加载更多**  
`void setMore(final int res,OnLoadMoreListener listener)`  
`void setMore(final View view,OnLoadMoreListener listener)`  
注意一定当添加0条数据或null时,会结束加载更多,显示没有更多。  
也可以在最后一页手动调用`adapter.stopMore();`  
 
**加载错误**  
`View setError(final int res)`  
`View setError(final View view)`  
`adapter.pauseMore()`暂停加载更多，显示错误View。暂停时如果再次添加数据。自动恢复加载更多。  
`adapter.resumeMore()`继续加载更多，显示加载更多View，并立即回调加载更多。  

**没有更多**  
在adapter里设置，当停止加载后就会显示在最后一个。  
`void setNoMore(final int res)`   
`void setNoMore(final View view)`  

##BaseViewHolder\<M\>
这个ViewHolder将每个item与adapter解耦。adapter只管实例化对应ViewHolder.每个Item的view生成,findviewbyid,UI修改都由viewHolder自己管理。  
列如:

        public class PersonViewHolder extends BaseViewHolder<Person> {
            private TextView mTv_name;
            private SimpleDraweeView mImg_face;
            private TextView mTv_sign;
        
        
            public PersonViewHolder(ViewGroup parent) {
                super(parent,R.layout.item_person);
                mTv_name = $(R.id.person_name);
                mTv_sign = $(R.id.person_sign);
                mImg_face = $(R.id.person_face);
            }
        
            @Override
            public void setData(final Person person){
                mTv_name.setText(person.getName());
                mTv_sign.setText(person.getSign());
                mImg_face.setImageURI(Uri.parse(person.getFace()));
            }
        }


**详细用法请看dome，哦不~demo**






