# vue-pagination

### 使用方式

1. import vPagination from 'v_pagination.vue';

2. 
    ```
    components: {
        vPagination
    }
    ```

3. 

    ```
    <v-pagination :cur_page.sync="your_current_page" :total_num.sync="your_total_data_rows" :page_size.once="your_per_page_size":callback="your_callback_when_page_swith" :num_links="how_many_page_number_button_display"></v-pagination>
    ```

4. DEMO 效果图
    ![DEMO 效果图](http://h0.hucdn.com/open/201620/1463478303_255e64e0ff8b50ef_802x198.png)