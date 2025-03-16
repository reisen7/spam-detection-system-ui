<template>
    <div>
        <el-input placeholder="请选择" :size="size" :disabled="inpDisabled" style="line-hight:40px" v-model="name" class="input-with-select">
            <el-button slot="append" :disabled="btnDisabled" @click="showUserSelect" icon="el-icon-search"></el-button>
        </el-input>
        <UserInfoSelectDialog
                ref="userInfoSelect"
                @doSubmit="selectionsToInput"
                :selectData="selectData"
                 :where="where"
        />
    </div>
</template>
<script>
    import UserInfoSelectDialog from './operationLogUserInfoSelectOneDialog'
    import userInfoApi from '@/api/business/userInfo'
    export default {
        data () {
            return {
                name: '',
                selectData: {
                    type: Object,
                    default: () => {
                        return {};
                    },
                },
            }
        },
        props: {
            size: {
                type: String,
                default: 'small'
            },
            myvals: {
                type: String,
                default: ''
            },
            btnDisabled: {
                type: Boolean,
                default: false
            },
            inpDisabled: {
                type: Boolean,
                default: true
            },
            where: {  // 查询条件
                type: String,
                default: ''
            }
        },
        components: {
            UserInfoSelectDialog
        },
        created () {
        },
        watch: {
            myvals: {
                handler (newVal) {
                    this.selectData = {}
                    if (newVal && newVal.length > 0) {
                        userInfoApi.getById(newVal).then(
                            response => {
                                if (response && response.data) {
                                    this.selectData = response.data
                                }
                            }
                        )
                    }
                },
                immediate: true,
                deep: false
            },
            selectData: {
                handler (newVal) {
                    this.name = newVal.username
                },
                immediate: false,
                deep: false
            }
        },
        methods: {
            // 设置选中
            selectionsToInput (selections) {
                this.selectData = selections
                this.$emit('getInfo', this.selectData.user_id)
            },
            // 显示列表
            showUserSelect () {
                this.$refs.userInfoSelect.init()
            }
        }
    }
</script>
<style>
    .el-form-item__content .el-input-group {
        vertical-align: middle;
    }
    .el-tag + .el-tag {
        margin-left: 5px;
        margin-bottom: 5px;
    }
</style>
