<template>
        <div class="app-container">
        <div class="search-div">
            <el-form label-width="70px" size="small">
                <el-row>
                            <el-col :span="8">
                                <el-form-item label="日志唯一标识">
                                        <el-input v-model="searchObj.logId" placeholder="日志唯一标识" clearable/>
                                </el-form-item>
                            </el-col>
                            <el-col :span="8">
                                <el-form-item label="操作用户的唯一标识">
                                        <el-input v-model="searchObj.userName"
                                                  placeholder="用户名" clearable/>
                                </el-form-item>
                            </el-col>
                            <el-col :span="8">
                                <el-form-item label="操作类型">
                                        <el-input v-model="searchObj.operationType" placeholder="操作类型" clearable/>
                                </el-form-item>
                            </el-col>
                            <el-col :span="8">
                                <el-form-item label="操作详情">
                                        <el-input v-model="searchObj.operationDetail" placeholder="操作详情" clearable/>
                                </el-form-item>
                            </el-col>
                </el-row>

                <el-row style="display:flex">
                    <el-button type="primary" icon="el-icon-search" size="mini" @click="fetchData()">搜索</el-button>
                    <el-button icon="el-icon-refresh" size="mini" @click="resetData">重置</el-button>
                </el-row>
            </el-form>
        </div>
        <!-- 工具条 -->
        <div class="tools-div">
            <el-button type="success" icon="el-icon-plus" size="mini" @click="add" :disabled="$hasBP('bnt.operationLog.add')  === false">添 加</el-button>
            <el-button class="btn-add" size="mini" @click="batchRemove()" :disabled="$hasBP('bnt.operationLog.remove')  === false">批量删除</el-button>
            <el-button style="margin-left:8px" size="mini" :disabled="$hasBP('bnt.operationLog.list')  === false" type="primary" @click="exportData">导出数据</el-button>
        </div>
        <!-- 列表 -->
            <el-table
                    v-loading="listLoading"
                    :data="list"
                    :row-key="getRowKeys"
                    stripe
                    border
                    style="width: 100%;margin-top: 10px;"
                    @selection-change="handleSelectionChange">
            <el-table-column
                    label="序号"
                    width="70"
                    align="center">
                <template slot-scope="scope">
                    {{ (page - 1) * limit + scope.$index + 1 }}
                </template>
            </el-table-column>
            <el-table-column type="selection"  :reserve-selection="true"/>
                        <el-table-column sortable prop="logId" label="日志唯一标识"/>
                            <el-table-column sortable prop="userName" label="用户名"/>
                        <el-table-column sortable prop="operationTime" label="操作时间"/>
                        <el-table-column sortable prop="operationType" label="操作类型"/>
                        <el-table-column sortable prop="operationDetail" label="操作详情"/>
                        <el-table-column sortable prop="createTime" label="创建时间"/>
                        <el-table-column sortable prop="updateTime" label="修改时间"/>
                                <el-table-column type="expand" label="...">
                                    <template slot-scope="props">
                                        <el-form label-position="left" inline class="demo-table-expand">
                                                    <el-form-item label="日志唯一标识">
                                                        <span>{{ props.row.logId }}</span>
                                                    </el-form-item>
                                                        <el-form-item label="用户名">
                                                            <span>{{ props.row.userName }}</span>
                                                        </el-form-item>
                                                    <el-form-item label="操作时间">
                                                        <span>{{ props.row.operationTime }}</span>
                                                    </el-form-item>
                                                    <el-form-item label="操作类型">
                                                        <span>{{ props.row.operationType }}</span>
                                                    </el-form-item>
                                                    <el-form-item label="操作详情">
                                                        <span>{{ props.row.operationDetail }}</span>
                                                    </el-form-item>
                                                    <el-form-item label="创建时间">
                                                        <span>{{ props.row.createTime }}</span>
                                                    </el-form-item>
                                                    <el-form-item label="修改时间">
                                                        <span>{{ props.row.updateTime }}</span>
                                                    </el-form-item>
                                        </el-form>
                                    </template>
                                </el-table-column>
            <el-table-column label="操作" align="center" fixed="right">
                <template slot-scope="scope">
                    <el-button type="primary" icon="el-icon-edit" size="mini" @click="edit(scope.row.id)" :disabled="$hasBP('bnt.operationLog.update')  === false" title="修改"/>
                    <el-button type="danger" icon="el-icon-delete" size="mini" @click="removeDataById(scope.row.id)"
                               :disabled="$hasBP('bnt.operationLog.remove')  === false"     title="删除"/>
                </template>
            </el-table-column>
        </el-table>
        <!-- 分页组件 -->
        <el-pagination
                @size-change="sizeChangeHandle"
                :current-page="page"
                :total="total"
                :page-size="limit"
                style="padding: 30px 0; text-align: center;"
                :page-sizes="[5, 10, 50, 100]"
                layout="total,sizes, prev, pager, next, jumper"
                @current-change="fetchData"
        />
        <el-dialog title="添加/修改" :visible.sync="dialogVisible" width="40%" :close-on-click-modal="false">
            <el-form ref="dataForm" :model="operationLog" :rules="rules" label-width="100px" size="small"
                     style="padding-right: 40px;">
                    <el-form-item label="日志唯一标识" prop="logId">
                                <el-input v-model="operationLog.logId" clearable/>
                    </el-form-item>
                    <el-form-item label="操作用户的唯一标识" prop="userId">
                                    <UserInfoSelectOne
                                            :myvals="operationLog.userId"
                                            @getInfo="getUserInfoXf71c6e5b" where=""  />
                    </el-form-item>
                    <el-form-item label="操作时间" prop="operationTime">
                                <el-date-picker
                                        v-model="operationLog.operationTime"
                                        type="datetime"
                                        value-format="yyyy-MM-dd HH:mm:ss"
                                        placeholder="选择日期">
                                </el-date-picker>
                    </el-form-item>
                    <el-form-item label="操作类型" prop="operationType">
                                <el-input v-model="operationLog.operationType" clearable/>
                    </el-form-item>
                    <el-form-item label="操作详情" prop="operationDetail">
                                <el-input v-model="operationLog.operationDetail" clearable/>
                    </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false" size="small" icon="el-icon-refresh-right">取 消</el-button>
        <el-button type="primary" icon="el-icon-check" @click="saveOrUpdate()" size="small">确 定</el-button>
      </span>
        </el-dialog>
    </div>
</template>
<script>
    import {getToken} from '@/utils/auth'
    import api from '@/api/business/operationLog'
    import UserInfoSelectOne from './operationLogUserInfoSelectOne'
    const defaultForm = {
    }
    export default {
        components: {
            UserInfoSelectOne,
        },
        data() {
            return {
                listLoading: true, // 数据是否正在加载
                list: [], // banner列表
                total: 0, // 数据库中的总记录数
                page: 1, // 默认页码
                limit: 5, // 每页记录数
                searchObj: {}, // 查询表单对象
                dialogVisible: false,
                operationLog:defaultForm,
                saveBtnDisabled:false,
                selectValue:[] //复选框选择内容封装数组
                , rules:{
            }
        }
        },
        // 生命周期函数：内存准备完毕，页面尚未渲染
        created() {
            console.log('list created......')
            this.fetchData()
        },
        // 生命周期函数：内存准备完毕，页面渲染成功
        mounted() {
            console.log('list mounted......')
        },
        methods: {
            getRowKeys(row) {
                return row.id
            },
            //导出数据
            exportData() {
                window.open(process.env.VUE_APP_BASE_API + '/business/operationLog/export?token=' + getToken());
            },
            // 每页数
            sizeChangeHandle(val) {
                this.limit = val;
                this.page = 1;
                this.fetchData(this.page);
            },
            getUserInfoXf71c6e5b(selections) {
                this.operationLog.userId = selections
                this.$refs["dataForm"].clearValidate("userId")
                this.rules.userId = []
            },
            // 加载banner列表数据
            fetchData(page = 1) {

                this.page = page
                api.getPageList(this.page, this.limit, this.searchObj).then(
                    response => {
                        //this.list = response.data.list
                        this.list = response.data.records
                        this.total = response.data.total
                        // 数据加载并绑定成功
                        this.listLoading = false
                    }
                )
            },
            // 重置查询表单
            resetData() {
                console.log('重置查询表单')
                this.searchObj = {}
                this.fetchData()
            },
            // 根据id删除数据
            removeDataById(id) {
                // debugger
                this.$confirm('此操作将永久删除该记录, 是否继续?', '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    type: 'warning'
                }).then(() => { // promise
                    // 点击确定，远程调用ajax
                    return api.removeById(id)
                }).then((response) => {
                    this.fetchData(this.page)
                    this.$message.success(response.message || '删除成功')
                }).catch(() => {
                    this.$message.info('取消删除')
                })
            },
            //复选框发生变化执行方法
            handleSelectionChange(selection) {
                this.selectValue = selection
                //console.log(this.selectValue)
            },
            //批量删除
            batchRemove() {
                //判断
                if (this.selectValue.length == 0) {
                    this.$message.warning('请选择要删除的记录！')
                    return
                }
                this.$confirm('此操作将永久删除, 是否继续?', '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    type: 'warning'
                }).then(() => {
                    //数组
                    var idList = []
                    //获取多个复选框对应id，封装到数组里面
                    // [1,2,3]
                    for (var i = 0; i < this.selectValue.length; i++) {
                        var obj = this.selectValue[i]
                        //id值
                        var id = obj.id
                        //放到数组里面
                        idList.push(id)
                    }
                    api.batchRemove(idList).then(response => {
                        //提示
                        this.$message({
                            type: 'success',
                            message: '删除成功!'
                        });
                        //刷新
                        this.fetchData()
                    })
                })
            },
            //弹出添加表单
            add(row) {
                this.dialogVisible = true
                if(this.$refs.zdept){
                    this.$refs.zdept.reset_data()
                }
                this.operationLog = {
                }
            },
            //编辑
            edit(id) {
                this.dialogVisible = true
                api.getById(id).then(response => {
                    this.$nextTick(function () {
                        this.operationLog = response.data
                        if(this.$refs.zdept){
                            this.$refs.zdept.reset_data(this.operationLog.deptName)
                        }
                        if(this.$refs.eltreeDept){
                            this.$refs.eltreeDept.setCheckedKeys(this.operationLog.deptIdList);
                        }
                    })
                })
            },
            //添加或更新
            saveOrUpdate() {
                this.$refs.dataForm.validate(valid => {
                    if (valid) {
                        if (!this.operationLog.id) {
                            this.save()
                        } else {
                            this.update()
                        }
                    }
                })
            },
            //添加
            save() {
                api.save(this.operationLog).then(response => {
                    this.$message.success('操作成功')
                    this.dialogVisible = false
                    this.fetchData(this.page)
                })
            },
            //更新
            update() {
                api.updateById(this.operationLog).then(response => {
                    this.$message.success(response.message || '操作成功')
                    this.dialogVisible = false
                    this.fetchData(this.page)
                })
            }
        }
    }
</script>
<style>
    .demo-table-expand {
        font-size: 0;
    }
    .demo-table-expand label {
        width: 90px;
        color: #99a9bf;
    }
    .demo-table-expand .el-form-item {
        margin-right: 0;
        margin-bottom: 0;
        width: 50%;
    }
</style>
