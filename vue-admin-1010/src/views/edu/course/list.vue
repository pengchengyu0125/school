<template>
    <div class="app-container">
        <el-form :inline="true" class="demo-form-inline">
            <el-form-item>
                <el-input v-model="courseQuery.title" placeholder="课程名称"/>
            </el-form-item>

            <el-form-item>
                <el-select v-model="courseQuery.status" clearable placeholder="课程状态">
                    <el-option
                    v-for="item in options"
                    :key="item.value"
                    :label="item.label"
                    :value="item.value">
                    </el-option>
                </el-select>
            </el-form-item>
            
            <el-form-item label="添加时间">
                <el-date-picker
                v-model="courseQuery.begin"
                type="datetime"
                placeholder="选择开始时间"
                value-format="yyyy-MM-dd HH:mm:ss"
                default-time="00:00:00"
                />
            </el-form-item>

            <el-form-item>
                <el-date-picker
                v-model="courseQuery.end"
                type="datetime"
                placeholder="选择截止时间"
                value-format="yyyy-MM-dd HH:mm:ss"
                default-time="00:00:00"
                />
            </el-form-item>

            <el-button type="primary" icon="el-icon-search" @click="getList()">查询</el-button>
            <el-button type="default" @click="resetData()">清空</el-button>
        </el-form>
        <el-table
        v-loading="listLoading"
        :data="list"
        element-loading-text="数据加载中"
        border
        fit
        highlight-current-row>

        <el-table-column
            label="序号"
            width="70"
            align="center">
            <template slot-scope="scope">
            {{ (page - 1) * limit + scope.$index + 1 }}
            </template>
        </el-table-column>

        <el-table-column prop="title" label="课程名称" width="80" />
        
        <el-table-column label="课程状态" width="80">
            <template slot-scope="scope">
            {{ scope.row.status==='Normal'?'已发布':'未发布' }}
            </template>
        </el-table-column>
        
        <el-table-column prop="lessonNum" label="课时数" />
        
        <el-table-column prop="gmtCreate" label="添加时间" width="160"/>
        
        <el-table-column prop="viewCount" label="浏览数量" width="60" />
        
        <el-table-column label="操作" width="200" align="center">
            <template slot-scope="scope">
            <router-link :to="'/teacher/edit/'+scope.row.id">
                <el-button type="primary" size="mini" icon="el-icon-edit">编辑课程信息</el-button>
            </router-link>
            <router-link :to="'/teacher/edit/'+scope.row.id">
                <el-button type="primary" size="mini" icon="el-icon-edit">编辑课程大纲</el-button>
            </router-link>
            <el-button type="danger" size="mini" icon="el-icon-delete" @click="removeDataById(scope.row.id)">删除</el-button>
            </template>
        </el-table-column>
        </el-table>
        <el-pagination
            :current-page="page"
            :page-size="limit"
            :total="total"
            style="padding: 30px 0; text-align: center;"
            layout="total, prev, pager, next, jumper"
            @current-change="getList"
        />
    </div>
</template>

<script>
import course from '@/api/edu/course'

export default {
    data() {
        return {
            list:null,//查询后接口返回集合
            page:1,//当前页
            limit:5,//每页记录数
            total:0,//总记录数
            courseQuery:{},//条件封装对象
            options: [{
                value: 'Normal',
                label: '已发布'
            }, {
                value: 'Draft',
                label: '未发布'
            }]
        }
    },
    created() {
        this.getList()
    },
    methods:{
        //删除课程
        removeDataById(id) {
            this.$confirm('此操作将永久删除该课程, 是否继续?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
            }).then(() => {
                course.deleteCourse(id)
                .then(response =>{
                    this.$message({
                        type: 'success',
                        message: '删除成功!'
                    });
                    this.getList()
                })
            })
        },
        //讲师列表
        getList(page = 1) {
            this.page = page
            course.getListCourse(this.page,this.limit,this.courseQuery)
            .then(response =>{
                this.list = response.data.list
                this.total = response.data.total
            }) //请求成功
        },
        resetData() { //清空方法
            this.courseQuery = {}
            //查询所有数据
            this.getList()
        }
    }
}
</script>