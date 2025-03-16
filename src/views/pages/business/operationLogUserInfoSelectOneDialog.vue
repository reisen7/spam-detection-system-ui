<template>
        <el-dialog
                title="用户"
                width="1000px"
                :close-on-click-modal="false"
                :append-to-body="true"
                v-dialogDrag
                :visible.sync="visible">
           <span slot="title" class="dialog-footer">
          <el-button
                  size="small"
                  @click="visible = false"
                  icon="el-icon-circle-close"
          >关闭</el-button>
          <el-button
                  size="small"
                  type="primary"
                  icon="el-icon-circle-check"
                  @click="doSubmit()"
          >确定</el-button>
        </span>
                <div class="search-div" style="height: 55px;margin-top: -25px">
                    <el-form
                            size="small"
                            ref="searchForm"
                            :model="searchForm">
                        <el-row>
                                    <el-col :span="5" style="margin-left: 5px">
                                        <el-form-item prop="username">
                                            <el-input
                                                    size="small"
                                                    v-model="searchForm.username"
                                                    placeholder="用户名"
                                                    @input="changeInput($event)"
                                                    clearable>
                                            </el-input>
                                        </el-form-item>
                                    </el-col>
                                    <el-col :span="5" style="margin-left: 5px">
                                        <el-form-item prop="password">
                                            <el-input
                                                    size="small"
                                                    v-model="searchForm.password"
                                                    placeholder="用户密码"
                                                    @input="changeInput($event)"
                                                    clearable>
                                            </el-input>
                                        </el-form-item>
                                    </el-col>
                                    <el-col :span="5" style="margin-left: 5px">
                                        <el-form-item prop="role">
                                            <el-input
                                                    size="small"
                                                    v-model="searchForm.role"
                                                    placeholder="用户角色"
                                                    @input="changeInput($event)"
                                                    clearable>
                                            </el-input>
                                        </el-form-item>
                                    </el-col>
                            <el-col :span="5" style="margin-left: 5px">
                                <el-form-item>
                                    <el-button
                                            type="primary"
                                            @click="refreshList()"
                                            size="small"
                                            icon="el-icon-search">搜索
                                    </el-button>
                                    <el-button
                                            @click="resetSearch()"
                                            size="small"
                                            icon="el-icon-refresh-right">重置
                                    </el-button>
                                </el-form-item>
                            </el-col>
                        </el-row>
                    </el-form>
                </div>
                <el-table
                        :data="dataList"
                        v-loading="loading"
                        size="small"
                        border
                        ref="userInfoTable"
                        highlight-current-row
                        @current-change="propertySelectionChange"
                        style="width: 100%;margin-top: 10px;">
                                    <el-table-column sortable prop="userName" label="用户唯一标识名称"/>
                                <el-table-column sortable prop="username" label="用户名"/>
                                <el-table-column sortable prop="password" label="用户密码"/>
                                <el-table-column sortable prop="role" label="用户角色"/>
                                <el-table-column sortable prop="createTime" label="创建时间"/>
                                <el-table-column sortable prop="updateTime" label="修改时间"/>
                </el-table>
                <el-pagination
                        @size-change="sizeChangeHandle"
                        @current-change="currentChangeHandle"
                        :current-page="pageNo"
                        :page-sizes="[5, 10, 50, 100]"
                        :page-size="pageSize"
                        :total="total"
                        style="padding: 30px 0; text-align: center;"
                        layout="total, sizes, prev, pager, next, jumper"
                >
                </el-pagination>
        </el-dialog>
</template>
<script>
    import userInfoApi from '@/api/business/userInfo'

    export default {
        data() {
            return {
                searchForm: {},
                dataListAllSelections: {}, // 所有选中的数据包含跨页数据
                dataList: [],
                pageNo: 1,
                pageSize: 5,
                total: 0,
                loading: false,
                visible: false,
            };
        },
        props: {
            selectData: {
                type: Object,
                default: () => {
                    return {};
                },
            },

            where: {
                type: String,
                default: ''
            }
        },
        methods: {
            changeInput() {
                this.$forceUpdate();
            },

            init() {
                this.visible = true;
                this.$nextTick(() => {
                    this.dataListAllSelections = this.selectData;
                    this.$refs.userInfoTable.clearSelection()
                    this.resetSearch();
                });
            },
            // 获取数据列表
            refreshList() {
                if(this.where != null && this.where != ''){
                    let queryObj = JSON.parse(this.where)
                   this.searchForm = Object.assign(queryObj,this.searchForm)
                }
                userInfoApi.getPageList(this.pageNo, this.pageSize, this.searchForm).then(
                    response => {
                        //this.list = response.data.list
                        this.dataList = response.data.records
                        this.total = response.data.total
                        // 数据加载并绑定成功
                        this.loading = false
                    }
                )
            },
            // 每页数
            sizeChangeHandle(val) {
                this.pageSize = val;
                this.pageNo = 1;
                this.refreshList();
            },
            // 当前页
            currentChangeHandle(val) {
                this.pageNo = val;
                this.refreshList();
            },
            // 排序
            resetSearch() {
                this.$refs.searchForm.resetFields();
                this.refreshList();
            },
            //监听选中
            propertySelectionChange(newVal,oldVal) {
                this.dataListAllSelections = newVal
            },
            doSubmit() {
                this.visible = false;
                this.$emit("doSubmit", this.dataListAllSelections);
            },
        },
    };
</script>
<style lang="scss">
</style>
