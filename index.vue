<!--
 * @Description: 
 * @Author: ydhu5
 * @Date: 2021-11-12 11:02:48
 * @LastEditTime: 2021-12-02 16:42:32
 * @LastEditors: ydhu5
-->
<template>
  <div>
    <div v-if="$route.name == 'coursePackages'">
      <div class="content1">
        <div class="left">
          <div>
            <el-input
              placeholder="请输入课程包名称"
              style="width: 100%"
              v-model.trim="word1"
              @clear="searchD()"
            >
              <el-button
                slot="append"
                icon="el-icon-search"
                @click="searchD()"
              ></el-button>
            </el-input>
            <div class="head">
              <div class="title">课程包</div>
              <!-- <el-button @click="click()" type="text">创建</el-button> -->
              <i
                class="el-icon-circle-plus-outline"
                @click="click()"
                style="margin-top: 15px; cursor: pointer; color: #004ea2ff"
              ></i>
            </div>
          </div>

          <div
            v-for="(item, index) in list"
            :key="item.id"
            :class="{ checked: item.checked }"
            @click="changeU(index)"
            class="li"
            @mouseenter="showIcon(true, item)"
            @mouseleave="showIcon(false, item)"
          >
            <div class="name">
              <el-tooltip
                class="item"
                effect="dark"
                :content="item.coursePackageName"
                placement="top"
              >
                <span>{{ item.coursePackageName }}</span>
              </el-tooltip>
            </div>
            <div v-if="item.show">
              <!-- <el-button type="text" @click="editD(item)">编辑</el-button> -->
              <i
                class="el-icon-edit-outline buttonv i1"
                @click="editD(item)"
              ></i>
              <!-- <el-button @click="deleteD(item)" type="text">删除</el-button> -->
              <i
                class="el-icon-delete buttonv i2"
                @click="deleteD(item)"
                style="margin-left: 10px"
              ></i>
            </div>
          </div>
        </div>
        <div class="right">
          <div class="head">
            <span class="left-line-title">课程列表</span>
            <el-button
              @click="
                chooseV();
                listCourseType();
              "
              :disabled="list.length == 0"
              style="height: 34px; width: 100px; float: right"
              type="primary"
              >选择课程</el-button
            >
          </div>
          <div v-if="showCourse.length != 0">
            <el-table :data="showCourse">
              <el-table-column label="序号" width="150" align="left">
                <template slot-scope="scope">
                  {{ scope.$index + 1 + (currentPage - 1) * size }}
                </template>
              </el-table-column>
              <el-table-column prop="courseName" label="课程名称">
              </el-table-column>
              <el-table-column prop="courseTypeName" label="课程类型">
              </el-table-column>
              <el-table-column label="操作" fixed="right" width="313">
                <template slot-scope="scope">
                  <el-button
                    @click="handleClick(scope.row)"
                    type="text"
                    size="small"
                    >详情</el-button
                  >
                  <el-button
                    type="text"
                    size="small"
                    style="color: red; margin-left: 20px"
                    @click="deleteCourse(scope.row)"
                    >删除</el-button
                  >
                </template>
              </el-table-column>
            </el-table>
            <div style="background: white; height: 100px; margin-top: 20px">
              <el-pagination
                background
                v-if="pageShow"
                style="margin-top: 20px; padding: 0"
                @size-change="handleSizeChange"
                @current-change="handleCurrentChange"
                :current-page.sync="currentPage"
                :page-sizes="[10, 20, 30, 40]"
                :page-size="size"
                layout="prev,pager,next,sizes,total,jumper"
                :total="total"
              >
              </el-pagination>
            </div>
          </div>
          <div v-else>
            <EmptyList :srcUrl="require('@/assets/empty/img_kc_empty.png')">
              <slot> 暂无数据 </slot>
            </EmptyList>
          </div>
        </div>
      </div>
      <el-dialog
        :title="coursePackageName ? '编辑课程包' : '新增课程包'"
        :visible.sync="drawer"
        width="30%"
        :before-close="handleClose"
      >
        <div style="height: 34px">
          <i style="color: #f0353cff">*</i
          ><span style="color: #313233ff">课程包名称：</span
          ><el-input
            style="width: 400px"
            v-model="coursePackageName"
            maxlength="30"
            show-word-limit
          ></el-input>
        </div>
        <span slot="footer" class="dialog-footer">
          <el-button @click="clearD()" plain>取 消</el-button>
          <el-button type="primary" @click="saveCoursePackageType()"
            >保 存</el-button
          >
        </span>
      </el-dialog>
      <el-drawer
        title="添加课程"
        :visible="all"
        size="760px;"
        direction="rtl"
        :before-close="handleClose2"
        :wrapperClosable="false"
        :show-close="false"
      >
        <div class="slider">
          <div class="head">
            <el-select
              v-model="selectV"
              @change="listCourseware(0)"
              :clearable="true"
            >
              <el-option label="全部课程类型" value=""> </el-option>
              <el-option
                v-for="item in selectType"
                :key="item.courseTypeId"
                :label="item.courseTypeName"
                :value="item.courseTypeId"
              >
              </el-option>
            </el-select>
            <el-input
              placeholder="请输入课程名称"
              v-model.trim="word2"
              style="width: 250px; margin-left: 20px"
              @clear="listCourseware('search')"
            >
              <el-button
                slot="append"
                icon="el-icon-search"
                style="height: 30px; width: 30px"
                @click="listCourseware('search')"
              ></el-button>
            </el-input>
          </div>
          <div class="middle">
            <el-table
              :data="allCourse"
              @selection-change="handleSelectionChange"
              key="id"
              ref="all"
              :row-key="getID"
            >
              <el-table-column
                type="selection"
                :reserve-selection="true"
                width="55"
                align="center"
                :selectable="checkSelectable"
              >
              </el-table-column>
              <el-table-column prop="courseName" label="课程名称" width="300">
              </el-table-column>
              <el-table-column prop="courseTypeName" label="课程类型">
              </el-table-column>
              <el-table-column prop="modifiedTime" label="更新时间">
                <template slot-scope="scope">
                  <div>{{ time(scope.row.modifiedTime) }}</div>
                </template>
              </el-table-column>
            </el-table>
            <div>
              <el-pagination
                background
                style="margin-top: 30px; padding: 0"
                @size-change="handleSizeChange2"
                @current-change="handleCurrentChange2"
                :current-page.sync="currentPage2"
                :page-sizes="[10, 20, 30, 40]"
                :page-size="size2"
                layout="prev,pager,next,sizes,total,jumper"
                :total="total2"
              >
              </el-pagination>
            </div>
          </div>

          <div class="foot">
            <el-button class="bt" @click="hidden()" plain>取消</el-button>
            <el-button @click="addCourse()" class="bt" type="primary"
              >确定</el-button
            >
          </div>
        </div>
      </el-drawer>
    </div>
    <router-view></router-view>
  </div>
</template>
<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import dayjs from 'dayjs';
import {
  listCoursePackageType,
  saveCoursePackageType,
  deleteCoursePackageType,
  listCourseInfo,
  listCourseware,
  addCourse,
  listCourseType,
  deleteCourse
} from '@/api/CoursePackages';
@Component({
  name: 'coursePackages'
})
export default class CoursePackages extends Vue {
  private word1 = '';
  private word2 = '';
  private coursePackageName = '';
  private coursePackageId = '';
  private selectV = '';
  private currentPage = 1;
  private total = 0;
  private size = 10;
  private list = [];
  private pageShow = true;
  private drawer = false;
  private selectCourse = [];
  private showCourse = [];
  private allCourse = [{ courseName: 1, id: 1, courseTypeName: '' }];
  private all = false;
  private multipleSelection = [];
  private selectType = [];
  private currentPage2 = 1;
  private size2 = 10;
  private total2 = 0;
  private getID(row) {
    return row.id; //这个id需要换成自己所绑定的Key
  }
  private handleSizeChange2(val) {
    this.currentPage2 = 1;
    this.size2 = val;
    this.listCourseware(0);
  }
  private handleCurrentChange2(val) {
    this.currentPage2 = val;
    this.listCourseware(0);
  }
  private handleClose() {
    this.drawer = false;
  }
  private click() {
    console.log(1111111);
    this.drawer = true;
  }
  private handleClick(row) {
    const v = this.list.filter((v) => {
      return v.checked;
    });
    console.log(v);

    this.$router.push({
      path: '/course/coursePackages/preview',
      query: {
        id: row.courseId,
        oldId: v[0].coursePackageId
      }
    });
  }
  private handleClose2() {
    this.all = false;
  }
  private handleSelectionChange(val) {
    this.multipleSelection = val;
  }
  private select = [];
  private chooseV(type) {
    this.all = true;
    this.currentPage2 = 1;
    this.size2 = 10;
    this.word2 = '';
    this.selectV = '';
    this.selectCourse.map((v) => {
      this.select.push(v.courseId);
    });

    const params = {
      pageNo: this.currentPage2,
      pageSize: this.size2,
      courseTypeId: this.selectV || '',
      courseName: this.word2 || ''
    };
    listCourseware(params).then((res) => {
      this.allCourse = res.data.records;
      this.total2 = res.data.total;
      this.$nextTick(() => {
        this.multipleSelection = [];
        (this.$refs.all as any).clearSelection();
        this.allCourse.map((v, index) => {
          if (this.select.includes(v.id)) {
            (this.$refs.all as any).toggleRowSelection(
              this.allCourse[index],
              true
            );
          }
        });
      });
    });
  }
  //获取课程

  private listCourseware(type) {
    if (type == 'search') {
      this.currentPage2 = 1;
      this.size2 = 10;
    }
    //选择课件
    const params = {
      pageNo: this.currentPage2,
      pageSize: this.size2,
      courseTypeId: this.selectV || '',
      courseName: this.word2 || ''
    };
    listCourseware(params).then((res) => {
      this.allCourse = res.data.records;
      this.total2 = res.data.total;
      this.$nextTick(() => {
        this.allCourse.map((v, index) => {
          if (this.select.includes(v.id)) {
            (this.$refs.all as any).toggleRowSelection(
              this.allCourse[index],
              true
            );
          }
        });
      });
    });
  }
  private checkSelectable(row) {
    return !this.select.includes(row.id);
  }
  private save() {}

  private changeU(index) {
    this.pageShow = false;
    this.select = [];
    this.size = 10;
    this.$nextTick(() => {
      this.pageShow = true;
    });

    this.currentPage = 1;

    this.list.map((v) => {
      v.checked = false;
    });
    this.list[index].checked = true;
    const id = this.list[index].coursePackageId;
    this.listCourseInfo(id, 1, 999);
  }
  private listCoursePackageType() {
    this.list = [];
    this.selectCourse = [];
    this.showCourse = [];
    this.total = 0;
    listCoursePackageType({ queryName: this.word1 }).then((v) => {
      console.log(v);
      if (v.code == 1000) {
        if (v.data.length == 0) {
          //判断
          // this.$nextTick(() => {
          //   this.selectCourse = [];
          // });
        }
        v.data.map((r, index) => {
          r.checked = false;
          r.show = false;
          this.list.push(r);
          if (index == 0) {
            this.list[0].checked = true;
            this.listCourseInfo(this.list[0].coursePackageId, 1, 999);
          }
        });
      }
    });
  }
  private listCoursePackageTypeOld() {
    this.list = [];
    this.selectCourse = [];
    this.showCourse = [];
    this.total = 0;
    listCoursePackageType({ queryName: this.word1 }).then((v) => {
      console.log(v);
      if (v.code == 1000) {
        v.data.map((r, index) => {
          r.checked = false;
          r.show = false;
          if (r.coursePackageId == this.$route.query.oldId) {
            r.checked = true;
          }
          this.list.push(r);
          this.listCourseInfo(this.$route.query.oldId, 1, 999);
          this.$router.push({ query: {} });
        });
      }
    });
  }
  private created() {
    if (this.$route.name == 'coursePackages') {
      if (this.$route.query.oldId) {
        this.listCoursePackageTypeOld();
      } else {
        this.listCoursePackageType();
      }
    }
  }
  private handleSizeChange(val) {
    console.log(val);
    this.size = val;
    this.currentPage = 1;
    this.showCourse = [];
    this.selectCourse.map((v, index) => {
      if (index < val) {
        this.showCourse.push(v);
      }
    });
  }
  private handleCurrentChange(val) {
    this.currentPage = val;
    this.showCourse = [];
    this.selectCourse.map((v, index) => {
      if (index >= (this.currentPage - 1) * this.size) {
        if (index < this.currentPage * this.size) {
          console.log(index, 888);
          this.showCourse.push(v);
        }
      }
    });
  }
  private saveCoursePackageType() {
    if (!this.coursePackageName) {
      this.$message.error('课程包名称不能为空！');
      return;
    }
    const params = {
      coursePackageName: this.coursePackageName,
      coursePackageTypeId: this.coursePackageId
    };
    this.$loading['show']();
    saveCoursePackageType(params).then((result) => {
      this.$loading['hide']();
      if (result.code == 1000) {
        this.$message.success('操作成功');
      }
      this.listCoursePackageType();
      this.clearD();
    });
  }
  private clearD() {
    this.drawer = false;
    this.coursePackageName = '';
    this.coursePackageId = '';
  }
  private hidden() {
    (this.$refs.all as any).clearSelection();
    this.all = false;
    this.select = [];
  }
  private editD(item) {
    window.event.stopPropagation();
    this.drawer = true;
    this.coursePackageName = item.coursePackageName;
    this.coursePackageId = item.coursePackageId;
  }
  private deleteD(item) {
    window.event.stopPropagation();
    const data = {
      coursePackageId: item.coursePackageId
    };
    this.$confirm('确定删除吗？', '提示', { confirmButtonClass: 'errorA' })
      .then(() => {
        deleteCoursePackageType(data).then((v) => {
          this.listCoursePackageType();
          this.$message.success('操作成功');
        });
      })
      .catch(() => {
        this.clearD();
      });
  }
  private listCourseInfo(id, page, limit) {
    this.showCourse = [];
    const params = {
      coursePackageId: id,
      pageNo: page,
      pageSize: limit
    };
    listCourseInfo(params).then((v) => {
      this.showCourse = [];
      this.selectCourse = v.data.records;
      this.total = v.data.total;
      //显示一部分
      this.selectCourse.map((v, index) => {
        if (index < 10) {
          this.showCourse.push(v);
        }
      });
    });
  }
  private searchD() {
    this.listCoursePackageType();
  }
  private table() {}
  private addCourse() {
    const data = {
      coursePackageId: '',
      courseIdList: []
    };
    this.list.map((v) => {
      if (v.checked) {
        data.coursePackageId = v.coursePackageId;
      }
    });
    console.log(this.multipleSelection);
    //this.selectCourse = this.multipleSelection;
    this.multipleSelection.map((v) => {
      if (!this.select.includes(v.id)) {
        data.courseIdList.push(v.id);
      }
    });
    this.$loading['show']();
    addCourse(data).then((res) => {
      this.$loading['hide']();
      this.all = false;
      this.currentPage = 1;
      this.size = 10;
      this.listCourseInfo(data.coursePackageId, 1, 999);
    });
  }
  private listCourseType() {
    listCourseType().then((res) => {
      this.selectType = res.data;
    });
  }
  private deleteCourse(row) {
    const params = {
      courseId: '',
      coursePackageId: ''
    };
    this.list.map((v) => {
      if (v.checked) {
        params.coursePackageId = v.coursePackageId;
      }
    });
    params.courseId = row.courseId;
    console.log(params);
    this.$confirm('确定删除吗？', '提示', { confirmButtonClass: 'errorA' })
      .then(() => {
        deleteCourse(params).then((v) => {
          if (v.code == 1000) {
            this.$message.success('操作成功');
            this.currentPage = 1;
            this.size = 10;
            this.selectCourse = [];
            this.listCourseInfo(params.coursePackageId, 1, 999);
          }
        });
      })
      .catch(() => {});
  }
  private time(val) {
    return dayjs(val).format('YYYY-MM-DD HH:mm:ss');
  }
  private showIcon(type, item) {
    item.show = type;
  }
}
</script>
<style lang="scss">
.el-table .none {
  display: none;
}
.errorA {
  background: $color-danger !important;
  border: none;
}
</style>
<style scoped lang="scss">
.content1 {
  position: absolute;
  width: 100%;
  height: 100%;
  padding: 0 20px;
  overflow: scroll;
  display: flex;
  .left {
    width: 260px;
    height: 93%;
    background: #ffffff;
    overflow: auto;
    padding: 20px 10px;
    font-size: 14px;
    border-right: 1px solid #ddd;
    .head {
      font-size: 16px;
      padding: 0 10px;
      color: #313233;
      display: flex;
      height: 44px;
      justify-content: space-between;
      line-height: 44px;
      margin-top: 10px;
      .title {
        color: #313233;
        font-size: 16px;
        height: 44px;
        line-height: 44px;
        font-weight: 600;
      }
    }
    .li:hover {
      cursor: pointer;
    }
    .li {
      height: 44px;
      line-height: 44px;
      padding: 0 10px;
      display: flex;
      justify-content: space-between;
      .name {
        width: 50%;
        overflow: hidden;
        white-space: nowrap;
        text-overflow: ellipsis;
      }
    }
  }
  .right {
    padding: 24px 24px;
    width: 90%;
    max-height: 93%;
    overflow: auto;
    // margin-left: 20px;
    background: #ffffff;
    .head {
      height: 50px;
      // display: flex;
      // justify-content: space-between;
    }
  }
}
.checked {
  color: #004ea2;
  background: rgba(0, 78, 162, 0.06);
}
.slider {
  padding: 20px;
  height: 100%;
  .head {
    height: 50px;
    line-height: 50px;
    margin-bottom: 10px;
  }
  .middle {
    height: 85%;
    overflow: auto;
  }
  .foot {
    margin-top: 10px;
    width: 100%;
    height: 50px;
    position: absolute;
    bottom: 0;
    border-top: 1px solid #e6e9edff;
    text-align: center;
    .bt {
      margin-top: 10px;
      width: 110px;
      height: 34px;
    }
  }
}
</style>
<style scoped lang="scss">
.el-pagination.is-background {
  padding: 0 !important;
  margin-left: -5px;
  ::v-deep {
    button,
    .el-pager li,
    .el-input__inner,
    span:not([class*='suffix']) {
      height: 32px;
      line-height: 32px;
    }
    .el-select .el-input {
      width: 92px;
      .el-input__inner {
        padding-left: 0px;
        padding-right: 18px;
      }
    }
    .btn-next,
    .btn-prev {
      background-color: $color-white !important;
      border: 1px solid $color-gray-border;
      padding: 0px 10px;
      border-radius: 5px;
    }
    .btn-next,
    .btn-prev,
    .el-pager li {
      min-width: 32px;
      background-color: $color-white;
      border: 1px solid $color-gray-border;
      border-radius: 5px;
      font-weight: normal;
    }
    .el-pager li.active {
      border-color: $color-primary;
    }
    .btn-prev,
    .btn-next {
      padding: 0 5px;
      i {
        font-weight: normal;
      }
      &:disabled {
        color: #d6d6d6;
      }
    }
  }
}
.buttonv {
  color: #94969a;
}
.i1:hover {
  color: #004ea2;
}
.i2:hover {
  color: #f54645;
}
</style>
<style>
.el-dialog__title {
  color: #313233ff;
}
.el-dialog__footer {
  border: none;
}
.el-dialog__header {
  border: 1px solid #e6e9edff;
}
.el-message-box__title {
  color: #313233ff;
}
.el-message-box__header {
  border-bottom: 1px solid #e6e9edff;
}
</style>
