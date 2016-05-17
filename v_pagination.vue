<template>
    <nav class="m-pagination-nav" v-if="total_pages >= 2">
        <ul class="pagination">
            
            <!-- 上一页 -->
            <li v-if="cur_page == 1" class="disabled">
                <a href="javascript:;" aria-label="Previous">
                    <span aria-hidden="true">&laquo;</span>
                </a>
            </li>
            <li v-else @click="changePage(cur_page - 1)">
                <a href="javascript:;" aria-label="Previous">
                    <span aria-hidden="true">&laquo;</span>
                </a>
            </li>

            <!-- 循环 -->
            <template v-for="n in arrList">
                <!-- 后缀点 -->
                <li v-if="end < total_pages - 1 && n == total_pages" class="disabled">
                    <a href="javascript:;">
                        <span aria-hidden="true">...</span>
                    </a>
                </li>

                <li class="active" v-if="isCurrent(n)">
                    <a href="javascript:;">{{n}} <span class="sr-only">(current)</span></a>
                </li>
                <li v-else>
                    <a href="javascript:;" @click="changePage(n)">{{n}}</a>
                </li>

                <!-- 前缀点 -->
                <li v-if="start > 2 && n == 1" class="disabled">
                    <a href="javascript:;">
                        <span aria-hidden="true">...</span>
                    </a>
                </li>
            </template>

            <!-- 下一页 -->
            <li v-if="cur_page == total_pages" class="disabled">
                <a href="javascript:;" aria-label="Next">
                    <span aria-hidden="true">&raquo;</span>
                </a>
            </li>
            <li v-else @click="changePage(cur_page + 1)">
                <a href="javascript:;" aria-label="Next">
                    <span aria-hidden="true">&raquo;</span>
                </a>
            </li>
        </ul>

        <!-- 跳转至 -->
        <template v-if="start > 2 || end < total_pages - 1">
            <form class="form-inline m-pagination-apply">
                <div class="form-group">
                    <label>跳转至</label>
                    <input type="text" maxlength="{{input_max_length}}" v-model="to_page_num" class="form-control u-input-page">
                </div>
                <a class="btn btn-default" @click="changePage(to_page_num)" href="javascript:;" role="button">跳转</a>
            </form>
        </template>
    </nav>
</template>

<script type="text/babel">
    export default{
        props: {
            // 总数据量
            total_num: {
                type: Number,
                required: true,
                twoWay: true,
                coerce(val){
                    return parseInt(val);
                },
                default: 0
            },
            // 当前页面
            cur_page: {
                type: Number,
                coerce(val){
                    return parseInt(val);
                },
                default: 1
            },
            // 每页多少条数据
            page_size: {
                type: Number,
                coerce(val){
                    return parseInt(val);
                },
                default: 20
            },
            // 展示几个分页数值
            num_links: {
                type: Number,
                twoWay: true,
                coerce(val){
                    return parseInt(val);
                },
                default: 7
            },
            // 切换分页时的回调函数 接受当前分页值作为参数
            callback: {
                type: Function,
                required: true
            },
        },
        data(){
            return {
                start: 0,
                end: 0,
                to_page_num: 1
            }
        },
        watch: {
            cur_page(){
                this.reset();
            },
            total_pages() {
                this.reset();
            }
        },
        computed: {
            // 总页数
            total_pages() {
                return Math.ceil(this.total_num / this.page_size);
            },

            // 要展示的“页”数量的一半
            padding() {
                return Math.floor(this.num_links / 2);
            },

            input_max_length() {
                return ('' + this.total_pages).length;
            },

            arrList() {
                let i = this.start;
                let arr = [];

                for (; i <= this.end; i += 1) {
                    arr.push(i);
                }

                arr.unshift(1);
                arr.push(this.total_pages);

                return arr;
            }
        },
        methods: {
            isCurrent(page) {
                return parseInt(this.cur_page) == parseInt(page);
            },
            changePage(page) {
                this.cur_page = page;
                this.callback();
            },
            reset() {
                let padding = this.padding;
                let less = 0;
                
                // 当前页限制
                if (this.cur_page > this.total_pages) {
                    this.cur_page = this.total_pages;
                }

                // 除了第一页和最后一页以外，中间显示的最后的页数
                if (this.cur_page - padding < 1) {
                    this.start = 2;
                } else {
                    this.start = this.cur_page - padding + 1;
                }

                // 除了第一页和最后一页以外，中间显示的开始的页数
                if (this.cur_page + padding > this.total_pages) {
                    this.end = this.total_pages - 1;
                } else {
                    this.end = this.cur_page + padding - 1;
                }
                
                // 算上第一页和最后一页，中间放置的“页按钮”数量不足
                if (this.end - this.start < this.num_links - 2) {
                    less = this.num_links - 2 - (this.end - this.start) - 1;
                    
                    if (this.start == 2) {
                        this.end = this.end + less < this.total_pages ? this.end + less : this.total_pages - 1;

                    } else if (this.end == this.total_pages - 1) {
                        this.start = this.start - less > 1 ? this.start - less : 2;
                    }
                }
            }
        }
    }
</script>
<style lang="less">
    .m-pagination-nav {
        text-align: center;
        .pagination {
            margin: 10px;
        }
        .u-input-page {
            text-align: center;
            width: 65px;
        }
        .m-pagination-apply {
            padding-bottom: 20px;
        }
    }
</style>