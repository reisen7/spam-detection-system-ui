<template>
  <div class="app-container">

    <!-- 工具条 -->
    <div class="tools-div">
      <el-button type="success" icon="el-icon-plus" :disabled="$hasBP('bnt.sysMenu.add')  === false" size="mini" @click="add()">添 加</el-button>
    </div>
    <el-table
      :data="sysMenuList"
      style="width: 100%;margin-bottom: 20px;margin-top: 10px;"
      row-key="id"
      border
      :default-expand-all="false"
      :tree-props="{children: 'children'}">

      <el-table-column prop="name" label="菜单名称" width="160"/>
      <el-table-column label="图标">
        <template slot-scope="scope">
          <i :class="scope.row.icon"></i>
        </template>
      </el-table-column>
      <el-table-column prop="perms" label="权限标识" width="160"/>
      <el-table-column prop="path" label="路由地址" width="120"/>
      <el-table-column prop="component" label="组件路径" width="160"/>
      <el-table-column prop="sortValue" label="排序" width="60"/>
      <el-table-column label="状态" width="80">
        <template slot-scope="scope">
          <el-switch
            v-model="scope.row.status === 1" :disabled="true">
          </el-switch>
        </template>
      </el-table-column>
      <el-table-column prop="createTime" label="创建时间" width="160"/>
      <el-table-column label="操作" width="180" align="center" fixed="right">
        <template slot-scope="scope">
          <el-button type="success" v-if="scope.row.type !== 2" icon="el-icon-plus" size="mini" @click="add(scope.row)"
                     title="添加下级节点" :disabled="$hasBP('bnt.sysMenu.add')  === false"/>
          <el-button type="primary" icon="el-icon-edit" size="mini" :disabled="$hasBP('bnt.sysMenu.update')  === false" @click="edit(scope.row)" title="修改"/>
          <el-button type="danger" icon="el-icon-delete" size="mini" :disabled="$hasBP('bnt.sysMenu.remove')  === false || scope.row.children.length > 0" @click="removeDataById(scope.row.id)" title="删除"
                    />
        </template>
      </el-table-column>
    </el-table>

    <el-dialog :title="dialogTitle" :visible.sync="dialogVisible" width="40%" :close-on-click-modal="false">
      <el-form ref="dataForm" :model="sysMenu" label-width="150px" size="small" style="padding-right: 40px;">
        <el-form-item label="上级菜单" >
          <!--          <el-input v-model="sysMenu.parentName" disabled="true"/>-->
          <select-menu-tree @saveTreeId="saveMenuId" treeTitle="选择菜单" :props="menuProps" :dataTree="dataMenuTree"
                            :treeVal="sysMenu.parentName" :treeId="sysMenu.parentId"></select-menu-tree>
        </el-form-item>
        <el-form-item label="菜单类型" prop="type">
          <el-radio-group v-model="sysMenu.type" :disabled="typeDisabled">
            <el-radio :label="0" :disabled="type0Disabled">目录</el-radio>
            <el-radio :label="1" :disabled="type1Disabled">菜单</el-radio>
            <el-radio :label="2" :disabled="type2Disabled">按钮</el-radio>
          </el-radio-group>
        </el-form-item>
        <el-form-item label="菜单名称" prop="name">
          <el-input v-model="sysMenu.name"/>
        </el-form-item>
        <el-form-item label="图标" prop="icon" v-if="sysMenu.type !== 2">
          <el-select v-model="sysMenu.icon" clearable>
            <el-option v-for="item in iconList" :key="item.class" :label="item.class" :value="item.class">
            <span style="float: left;">
             <i :class="item.class"></i>  <!-- 如果动态显示图标，这里添加判断 -->
            </span>
              <span style="padding-left: 6px;">{{ item.class }}</span>
            </el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="排序">
          <el-input-number v-model="sysMenu.sortValue" controls-position="right" :min="0"/>
        </el-form-item>
        <el-form-item prop="path">
              <span slot="label">
                <el-tooltip content="访问的路由地址，如：`sysUser`" placement="top">
                <i class="el-icon-question"></i>
                </el-tooltip>
                路由地址
              </span>
          <el-input v-model="sysMenu.path" placeholder="请输入路由地址"/>
        </el-form-item>
        <el-form-item prop="component" v-if="sysMenu.type !== 0">
              <span slot="label">
                <el-tooltip content="访问的组件路径，如：`system/user/index`，默认在`views`目录下" placement="top">
                <i class="el-icon-question"></i>
                </el-tooltip>
                组件路径
              </span>
          <el-input v-model="sysMenu.component" placeholder="请输入组件路径"/>
        </el-form-item>
        <el-form-item v-if="sysMenu.type === 2">
          <el-input v-model="sysMenu.perms" placeholder="请输入权限标识" maxlength="100"/>
          <span slot="label">
                <el-tooltip content="控制器中定义的权限字符，如：@PreAuthorize(hasAuthority('bnt.sysRole.list'))" placement="top">
                <i class="el-icon-question"></i>
                </el-tooltip>
                权限字符
              </span>
        </el-form-item>
        <el-form-item label="状态" prop="type">
          <el-radio-group v-model="sysMenu.status">
            <el-radio :label="1">正常</el-radio>
            <el-radio :label="0">停用</el-radio>
          </el-radio-group>
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
    import api from '@/api/system/sysMenu'

    const defaultForm = {
        id: '',
        parentId: '',
        name: '',
        type: 0,
        path: '',
        component: '',
        perms: '',
        icon: '',
        sortValue: 1,
        status: 1
    }
    export default {
        components: {
            //导入树形选择组件
            SelectMenuTree: () => import('@/components/Custom/TreeSelect/TreeSelect')
        },
        // 定义数据
        data() {
            return {
                sysMenuList: [],
                expandKeys: [], // 需要自动展开的项

                typeDisabled: false,
                type0Disabled: false,
                type1Disabled: false,
                type2Disabled: false,
                dialogTitle: '',

                dialogVisible: false,
                sysMenu: defaultForm,
                saveBtnDisabled: false,

                dataMenuTree: [], //树形选择数据
                menuProps: {
                    label: 'name',
                    children: 'children',
                    isLeaf: 'isLeaf'
                },

                iconList: [
                    {
                        class: "el-icon-s-tools",
                    },
                    {
                        class: "el-icon-s-custom",
                    },
                    {
                        class: "el-icon-setting",
                    },
                    {
                        class: "el-icon-user-solid",
                    },
                    {
                        class: "el-icon-s-help",
                    },
                    {
                        class: "el-icon-phone",
                    },
                    {
                        class: "el-icon-s-unfold",
                    },
                    {
                        class: "el-icon-s-operation",
                    },
                    {
                        class: "el-icon-more-outline",
                    },
                    {
                        class: "el-icon-s-check",
                    },
                    {
                        class: "el-icon-tickets",
                    },
                    {
                        class: "el-icon-s-goods",
                    },
                    {
                        class: "el-icon-document-remove",
                    },
                    {
                        class: "el-icon-warning",
                    },
                    {
                        class: "el-icon-warning-outline",
                    },
                    {
                        class: "el-icon-question",
                    },
                    {
                        class: "el-icon-info",
                    },
                  {
                    class: "iconfont icon-xs",
                  },
                  {
                    class: "iconfont icon-fenzu",
                  },
                  {
                    class: "iconfont icon-kucunguanli",
                  },
                  {
                    class: "iconfont icon-daimashengcheng",
                  },
                  {
                    class: "iconfont icon-zidianguanli",
                  },
                  {
                    class: "iconfont icon-shujuzidianfenlei",
                  },
                  {
                    class:"iconfont icon-xinxiguanli"
                  }
                ]
            }
        },

        //当页面加载时获取数据
        created() { //页面渲染之前执行
            this.fetchData()
            this.fetchPopMenu()
        },

        methods: {
            //调用api层获取数据库中的数据
            fetchData() {
                console.log('加载列表')
                api.findNodes().then(response => {
                    this.sysMenuList = response.data
                    console.log(this.sysMenuList)
                })
            },

            //回调保存选择的id
            saveMenuId(treeId) {
                if (treeId == this.sysMenu.id) {
                    this.$alert('上级菜单不能和当前菜单相同', '提示', {
                        confirmButtonText: '确定',
                        callback: action => {
                            this.sysMenu.parentId = '';
                            this.sysMenu.parentName = '';
                            api.getById(this.sysMenu.id).then(response => {
                                this.sysMenu = response.data
                            })
                        }
                    });
                } else {
                    this.sysMenu.parentId = treeId
                    this.sysMenu.parentName = treeName
                }
            },

            fetchPopMenu() {
                //加载弹出选择菜单数据
                api.findDir().then(response => {
                    this.dataMenuTree = response.data
                })

            },

            //根据id删除数据
            removeDataById(id) {
                // 
                this.$confirm('此操作将永久删除该记录, 是否继续?', '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    type: 'warning'
                }).then(() => { // promise
                    // 点击确定，远程调用ajax
                    return api.removeById(id)
                }).then((response) => {
                    this.fetchData()
                    this.$message({
                        type: 'success',
                        message: response.message
                    })
                }).catch(() => {
                    this.$message.info('取消删除')
                })
            },

            //弹出添加或更新的表单
            add(row) {
                
                this.typeDisabled = false
                this.dialogTitle = '添加下级节点'
                this.dialogVisible = true

                this.sysMenu = Object.assign({}, defaultForm)
                this.sysMenu.id = ''
                if (row) {
                    this.sysMenu.parentId = row.id
                    this.sysMenu.parentName = row.name
                    if (row.type === 0) {
                        this.sysMenu.type = 1
                        this.typeDisabled = false
                        this.type0Disabled = false
                        this.type1Disabled = false
                        this.type2Disabled = true
                    } else if (row.type === 1) {
                        this.sysMenu.type = 2
                        this.typeDisabled = true
                    }
                } else {
                    this.dialogTitle = '添加目录节点'
                    this.sysMenu.type = 0
                    this.sysMenu.component = 'Layout'
                    this.sysMenu.parentId = '0'
                    this.typeDisabled = true
                }
            },

            //编辑
            edit(row) {
                
                this.dialogTitle = '修改菜单'
                this.dialogVisible = true
                //调用接口查询
                api.getById(row.id)
                    .then(response => {
                        this.$nextTick(function () {
                            this.sysMenu = response.data
                            console.log('this.sysMenu')
                            console.log(this.sysMenu)
                            this.typeDisabled = true
                        })
                    })

            },

            //添加或更新
            saveOrUpdate() {
                if (this.sysMenu.type == 0 && this.sysMenu.parentId != '0') {
                    this.sysMenu.component = 'ParentView'
                }
                this.$refs.dataForm.validate(valid => {
                    if (valid) {
                        this.saveBtnDisabled = true // 防止表单重复提交
                        if (!this.sysMenu.id) {
                            this.save()
                        } else {
                            this.update()
                        }
                    }
                })
            },

            //添加
            save() {
                api.save(this.sysMenu).then(response => {
                    this.$message.success(response.message || '操作成功')
                    this.dialogVisible = false
                    this.fetchData(this.page)
                })
            },

            //更新
            update() {
                api.updateById(this.sysMenu).then(response => {
                    this.$message.success(response.message || '操作成功')
                    this.dialogVisible = false
                    this.fetchData()
                })
            }
        }
    }
</script>
