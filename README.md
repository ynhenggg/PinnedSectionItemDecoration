# Pinned section header decoration for RecyclerView. 「[中文版文档点这里](https://github.com/oubowu/PinnedSectionItemDecoration/blob/master/README-CN.md)」
A powerful pinned section header library. The realization of ideas comes from「[pinned-section-item-decoration](https://github.com/takahr/pinned-section-item-decoration)」. Please mark a star if you think it's helpful.（＾∀＾）
##  Function  
- Large pinned section header support vertical orientation of the LinearLayoutManager, GridLayoutManager and StaggeredGridLayoutManager.
- Small pinned section header support vertical orientation of the LinearLayoutManager and GridLayoutManager which its span count is 1.
- Support the header of the click, double click and long press event.
- Support the child view of the click, double click and long press event.
- It can draw the separator line and support custom separator line style. (PS: Vertical staggeredgrid layout requires fixed height, can not be randomly changed to lead to item position switch, refer to「[See line 99-115 of MainActivity](https://github.com/oubowu/PinnedSectionItemDecoration/blob/master/app%2Fsrc%2Fmain%2Fjava%2Fcom%2Foushangfeng%2Fpinneddemo%2FMainActivity.java)」)

## Screenshot
- Without separator line<br>
![大标签线性布局](/pic/big_header_linearlayout.gif) 
![大标签网格布局](/pic/big_header_gridlayout.gif) 
![大标签瀑布流布局](/pic/big_header_staggeredgridlayout.gif) 
![小标签线性布局](/pic/small_header_linearlayout.gif) 
![股市Demo](/pic/stock_demo.gif) 
- With separator line<br>
![大标签线性布局带分隔线](/pic/big_header_linear_divider.png) 
![大标签网格布局带分隔线](/pic/big_header_grid_divider.png) 
![大标签瀑布流布局带分隔线](/pic/big_header_staggeredgrid_divider.png) 
![小标签线性布局带分隔线](/pic/small_header_linear_divider.png) 

## How to use?

To add a dependency using Gradle:
```groovy
compile 'com.oushangfeng:PinnedSectionItemDecoration:1.1.1'
```

Adapter needs to implement the PinnedHeaderNotifyer interface then tells ItemDecoration which type is the pinned section header and the information of the pinned section header.「[See RecyclerAdapter](https://github.com/oubowu/PinnedSectionItemDecoration/blob/master/app%2Fsrc%2Fmain%2Fjava%2Fcom%2Foushangfeng%2Fpinneddemo%2Fadapter%2FRecyclerAdapter.java)」
```
    @Override
    public boolean isPinnedHeaderType(int viewType) {
        // TYPE_SECTION represents the type of pinned section header.
        return viewType == TYPE_SECTION;
    }
    
    @Override
    public T getPinnedHeaderInfo(int position) {
        return mData == null ? null : mData.get(position).getData();
    }
    
    
```
Adapter needs to process the span count of header through the FullSpanUtil.
```
    @Override
    public void onAttachedToRecyclerView(RecyclerView recyclerView) {
        FullSpanUtil.onAttachedToRecyclerView(recyclerView, this, TYPE_SECTION);
    }

    @Override
    public void onViewAttachedToWindow(RecyclerViewHolder holder) {
        FullSpanUtil.onViewAttachedToWindow(holder, this, TYPE_SECTION);
    }
```

To achieve large pinned section header, RecyclerView only need to add a PinnedHeaderItemDecoration. Note that the top layer of the header where the layout can not be set marginTop.「[See StockActivity](https://github.com/oubowu/PinnedSectionItemDecoration/blob/master/app%2Fsrc%2Fmain%2Fjava%2Fcom%2Foushangfeng%2Fpinneddemo%2FStockActivity.java)」
``` 

     final OnHeaderClickAdapter<StockEntity.StockInfo> clickAdapter = new OnHeaderClickAdapter<StockEntity.StockInfo>() {

         @Override
         public void onHeaderClick(int id, int position, StockEntity.StockInfo data) {
             switch (id) {
                 case OnItemTouchListener.HEADER_ID:
                     Toast.makeText(StockActivity.this, "点击了标签: " + mAdapter.getData().get(position).getPinnedHeaderName(), Toast.LENGTH_SHORT).show();
                     break;
                 case R.id.iv_more:
                     Toast.makeText(StockActivity.this, "点击了标签的更多按钮", Toast.LENGTH_SHORT).show();
                     break;
             }
         }

     };

     mRecyclerView.addItemDecoration(
             new PinnedHeaderItemDecoration.Builder<StockEntity.StockInfo>()
             // Set separator line resources id.
             .setDividerId(R.drawable.divider)
             // Enable draw the separator line, by default it's disable.
             .enableDivider(true)
             // Set click event for the header and its internal child view.
             .setClickIds(R.id.iv_more)
             // Enable header click event(not include the child view)， by default it's enable.
             .disableHeaderClick(false)
             // Set the listener. If the listener is not null and enable the header click event, then the callback return the header's id is ItemTouchListener.HEADER_ID.
             .setHeaderClickListener(clickAdapter)
             .create());
    
```
![大标签布局](/pic/big_pinned_header.png) 

To achieve small pinned section header is a little bit more complex, such as the layout A of the data .
```
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
              xmlns:tools="http://schemas.android.com/tools"
              android:layout_width="match_parent"
              android:layout_height="wrap_content"
              android:layout_marginBottom="2dp"
              android:layout_marginLeft="2dp"
              android:layout_marginRight="2dp"
              android:background="#70E593">

    <ImageView
        android:id="@+id/iv_animal"
        android:layout_width="match_parent"
        android:layout_height="120dp"
        tools:src="@mipmap/panda0"/>

    <TextView
        tools:text="1"
        android:id="@+id/tv_pos"
        android:textColor="#000000"
        android:textSize="18dp"
        android:padding="8dp"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"/>

</FrameLayout>
```
![布局A](/pic/item-data.png) 

This is a layout B with a small pinned section header.
```
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
             xmlns:tools="http://schemas.android.com/tools"
             android:layout_width="match_parent"
             android:layout_height="wrap_content"
             android:paddingBottom="2dp"
             android:paddingLeft="2dp"
             android:paddingRight="2dp"
             android:paddingTop="2dp">

    <ImageView
        android:id="@+id/iv_animal"
        android:layout_width="match_parent"
        android:layout_height="120dp"
        android:background="#70E593"
        tools:src="@mipmap/panda0"/>

    <TextView
        android:id="@+id/tv_small_pinned_header"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:background="#5A5A5A"
        android:padding="8dp"
        android:textColor="#ffffff"
        android:textSize="18dp"
        tools:text="熊猫"/>

</FrameLayout>
```
![布局B](/pic/small_pinned_header.png) 

The layout B add a header compare whit layout A，then RecyclerView only need to add a SmallPinnedHeaderItemDecoration. Note that the top layer of the header where the layout can not be set marginTop.「[See SecondActivity](https://github.com/oubowu/PinnedSectionItemDecoration/blob/master/app%2Fsrc%2Fmain%2Fjava%2Fcom%2Foushangfeng%2Fpinneddemo%2FSecondActivity.java)」
```
     OnHeaderClickAdapter<String> headerClickAdapter = new OnHeaderClickAdapter<String>() {

         @Override
         public void onHeaderClick(int id, int position, String data) {
             if (id == R.id.tv_small_pinned_header) {
                 Toast.makeText(SecondActivity.this, "点击了标签: " + data, Toast.LENGTH_SHORT).show();
             }
         }
     };
     mRecyclerView.addItemDecoration(
             // Constructor need to set the id of header.
             new SmallPinnedHeaderItemDecoration.Builder<String>(R.id.tv_small_pinned_header)
             // Enable draw the separator line, by default it's disable.
             .enableDivider(true)
             // Set separator line resources id.
             .setDividerId(R.drawable.divider)
             // Disable header click event(not include the child view)， by default it's enable.
             .disableHeaderClick(true)
             // Set click event for the header and its internal child view.
             // Here I disable header click event, but i set the header id, so the header click event will still happen.
             .setClickIds(R.id.tv_small_pinned_header)
             // Set the listener. If the listener is not null and enable the header click event, then the callback return the header's id is ItemTouchListener.HEADER_ID.
             .setHeaderClickListener(headerClickAdapter)
             .create());
    
```

## To be continued
- Solve the problem of setting the marginTop.
- Solve the problem of setting the marginBottom.

#### License
```
Copyright 2016 oubowu

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

   http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```




