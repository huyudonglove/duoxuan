<!--
 * @Description: 
 * @Author: ydhu5
 * @Date: 2021-11-15 10:06:54
 * @LastEditTime: 2021-12-02 16:21:38
 * @LastEditors: ydhu5
-->
<template>
  <div class="content" style="padding-top: 0">
    <div class="d1">
      <span class="bt" :class="{ state: state == 1 }" @click="choose(1)">
        <span :class="state == 1 ? 'numCheck' : 'num'">1</span>
        基础信息
        <i class="i" v-if="state == 1"></i
      ></span>
      <span class="bt" :class="{ state: state == 2 }" @click="choose(2)">
        <span :class="state == 2 ? 'numCheck' : 'num'">2</span>
        学习内容 <i class="i" v-if="state == 2"></i
      ></span>
      <span class="bt" :class="{ state: state == 3 }" @click="choose(3)">
        <span :class="state == 3 ? 'numCheck' : 'num'">3</span>
        课程设置 <i class="i" v-if="state == 3"></i
      ></span>
    </div>
    <div class="d2" v-if="state == 1">
      <div class="d21">
        <div class="title"><span class="left-line-title">基础信息</span></div>
        <el-form
          style="width: 70%"
          :model="ruleForm"
          :rules="rules"
          ref="ruleForm"
          label-width="100px"
          class="demo-ruleForm"
        >
          <el-form-item label="课程名称" prop="courseName">
            <el-input
              v-model.trim="ruleForm.courseName"
              maxlength="30"
              show-word-limit
              style="width: 80%"
              placeholder="请输入课程名称"
            ></el-input>
          </el-form-item>
          <el-form-item label="课程类型" prop="courseTypeId">
            <el-select
              v-model="ruleForm.courseTypeId"
              style="width: 35%"
              placeholder="请选择课程类型"
            >
              <el-option
                v-for="item in courseType"
                :key="item.courseTypeId"
                :label="item.courseTypeName"
                :value="item.courseTypeId"
              >
              </el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="课时" prop="courseHour">
            <el-select
              v-model="ruleForm.courseHour"
              style="width: 35%"
              placeholder="请选择课时"
            >
              <el-option
                v-for="item in hour"
                :key="item.id"
                :label="item.name"
                :value="item.id"
              >
              </el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="学习目标" prop="studyPurpose">
            <el-input
              type="textarea"
              placeholder="请输入学习目标"
              v-model="ruleForm.studyPurpose"
              maxlength="1000"
              show-word-limit
              :rows="6"
            ></el-input>
          </el-form-item>
        </el-form>
      </div>
      <div style="margin-top: 35px">
        <el-upload
          class="avatar-uploader"
          action="#"
          :show-file-list="false"
          :before-upload="beforeUpload"
        >
          <el-image
            v-if="coverUrl"
            :src="coverUrl"
            class="avatar"
            style="width: 267px; height: 175px"
          ></el-image>
          <i
            v-else
            class="el-icon-plus avatar-uploader-icon"
            style="width: 267px; height: 175px; margin-bottom: 20px"
          ></i>
        </el-upload>
        <el-upload
          style="display: inline"
          action="#"
          :show-file-list="false"
          :before-upload="beforeUpload"
          ref="upl"
        >
          <el-button type="text">上传封面</el-button>
        </el-upload>

        <span
          ><el-button
            type="text"
            style="margin-left: 40px"
            @click="imgBox = true"
            >选择封面</el-button
          ></span
        >
      </div>
      <div>
        <el-dialog title="选择封面" :visible.sync="imgBox" width="800px">
          <div class="imgBox">
            <div
              v-for="item in coverList"
              :key="item.url"
              :class="item.checked ? 'checked' : ''"
            >
              <el-image
                :src="item.url"
                style="width: 100%; height: 99%"
              ></el-image>
            </div>
          </div>
          <span slot="footer" class="dialog-footer">
            <el-button @click="imgBox = false">取 消</el-button>
            <el-button type="primary" @click="chooseImg()">确 定</el-button>
          </span>
        </el-dialog>
      </div>
      <ImgCropper
        v-if="cropperVisible"
        :imgInfo="imgInfo"
        :callback="uploadCover"
        @close="hideCropperDialog"
      ></ImgCropper>
    </div>

    <div v-if="state == 2" class="d3 containerC">
      <div class="title">
        <span class="left-line-title">学习内容设置</span
        ><el-button @click="chooseV(true)" type="primary" style="float: right">
          选择课件</el-button
        >
      </div>

      <div class="th header">
        <!-- <span class="type">排序</span> -->
        <span class="img" style="padding-left: 60px">名称</span>
        <span class="switch">学习时间（最短学习时长）</span>
        <span class="operate">操作</span>
      </div>
      <div class="nodata" v-if="selectCourse.length == 0">
        <div class="d">
          暂无课件，请先<el-button @click="chooseV(true)" type="text">
            选择课件</el-button
          >
        </div>
      </div>
      <div style="min-height: 80%">
        <draggable
          v-model="selectCourse"
          @update="datadragEnd"
          :options="{ animation: 500 }"
          handle=".drag"
        >
          <transition-group>
            <div
              v-for="(element, index) in selectCourse"
              :key="element.id"
              class="questionBox"
              style="background: white; padding: 0"
            >
              <div class="th list">
                <!-- <span class="type">
                <i
                  class="iconfont iconicon_drag pointer"
                  style="margin-right: 10px"
                ></i
                >{{ index + 1 }}
              </span> -->
                <span class="img">
                  <Iconfont
                    slot="prefix"
                    name="a-icon_drag1"
                    class="drag"
                    style="margin-right: 30px"
                  />
                  {{ element.coursewareName }}
                </span>
                <span class="switch">
                  <span class="span">
                    <el-input
                      v-model="element.h"
                      class="input"
                      :key="element.id"
                      :clearable="false"
                      @input="$forceUpdate()"
                      onkeyup="if(this.value.length==1){this.value=this.value.replace(/[^0-9]/g,'')}else{this.value=this.value.replace(/\D/g,'')}"
                      onafterpaste="if(this.value.length==1){this.value=this.value.replace(/[^0-9]/g,'')}else{this.value=this.value.replace(/\D/g,'')}"
                    ></el-input>
                    h
                  </span>
                  <span class="span">
                    <el-input
                      v-model="element.m"
                      class="input"
                      :key="element.id"
                      :clearable="false"
                      @input="$forceUpdate()"
                      onkeyup="if(this.value.length==1){this.value=this.value.replace(/[^0-9]/g,'')}else{this.value=this.value.replace(/\D/g,'')}"
                      onafterpaste="if(this.value.length==1){this.value=this.value.replace(/[^0-9]/g,'')}else{this.value=this.value.replace(/\D/g,'')}"
                    ></el-input
                    >m
                  </span>
                  <span class="span">
                    <el-input
                      v-model="element.s"
                      class="input"
                      :key="element.id"
                      :clearable="false"
                      @input="$forceUpdate()"
                      onkeyup="if(this.value.length==1){this.value=this.value.replace(/[^0-9]/g,'')}else{this.value=this.value.replace(/\D/g,'')}"
                      onafterpaste="if(this.value.length==1){this.value=this.value.replace(/[^0-9]/g,'')}else{this.value=this.value.replace(/\D/g,'')}"
                    ></el-input
                    >s
                  </span>
                </span>
                <span class="operate">
                  <el-button type="text" @click="openPreview(element)">
                    预览
                  </el-button>
                  <el-button
                    type="text"
                    style="color: red"
                    @click="deleteA(index)"
                  >
                    删除
                  </el-button>
                </span>
              </div>
            </div>
          </transition-group>
        </draggable>
      </div>

      <div>
        <el-drawer
          title="添加课件"
          :visible="all"
          size="760px"
          direction="rtl"
          :before-close="handleClose2"
          :wrapperClosable="false"
        >
          <div style="padding: 10px">
            <el-input
              placeholder="请输入课件名称"
              v-model.trim="queryName"
              style="width: 250px"
              @clear="searchK()"
            >
              <el-button
                slot="append"
                icon="el-icon-search"
                style="height: 30px; width: 30px"
                @click="searchK()"
              ></el-button>
            </el-input>
          </div>
          <div style="padding: 0 10px" class="middle">
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
              <el-table-column label="课件名称" prop="coursewareName">
              </el-table-column>
              <el-table-column label="更新时间" prop="modifiedTime">
                <template slot-scope="scope">
                  <div>{{ time(scope.row.modifiedTime) }}</div>
                </template>
              </el-table-column>
            </el-table>

            <div style="padding: 0 10px">
              <el-pagination
                background
                style="margin-top: 20px; padding: 0"
                @size-change="handleSizeChange"
                @current-change="handleCurrentChange"
                :current-page.sync="pageNo"
                :page-sizes="[10, 20, 30, 40]"
                :page-size="pageSize"
                layout="prev,pager,next,sizes,total,jumper"
                :total="total"
              >
              </el-pagination>
            </div>
          </div>

          <div class="foot">
            <el-button @click="cancel()" class="bt1" plain>取消</el-button>
            <el-button @click="save()" type="primary" class="bt1"
              >确定</el-button
            >
          </div>
        </el-drawer>
        <div style="height: 100px; line-height: 100px; text-align: center">
          <div v-if="state == 2">
            <el-button @click="$router.go(-1)" class="bt1" plain
              >取消</el-button
            >
            <el-button @click="go(-1)" class="bt1">上一步</el-button>
            <el-button @click="go(3)" type="primary" class="bt1"
              >下一步</el-button
            >
          </div>
        </div>
      </div>
    </div>
    <div v-if="state == 3" class="d3c">
      <div class="title"><span class="left-line-title">课程设置</span></div>
      <div class="for">
        <span class="span"> 顺序学习：</span>
        <el-switch
          v-model="isStudyInOrder"
          :active-value="1"
          :inactive-value="0"
        >
        </el-switch>
        <span class="word">开启后学员必须按照课件排列顺序学习</span>
      </div>
      <!-- <div class="for">
        <span class="span">放挂机设置：</span>

        <el-switch
          v-model="preventCheatEnabled"
          :active-value="1"
          :inactive-value="0"
        >
        </el-switch>
        学员学习时在
        <el-input
          v-model="preventCheatTime"
          style="width: 70px"
          :disabled="preventCheatEnabled == 0"
          :clearable="false"
          onkeyup="if(this.value.length==1){this.value=this.value.replace(/[^1-9]/g,'')}else{this.value=this.value.replace(/\D/g,'')}"
          onafterpaste="if(this.value.length==1){this.value=this.value.replace(/[^1-9]/g,'')}else{this.value=this.value.replace(/\D/g,'')}"
        ></el-input>
        分钟之内没有操作，将弹出提示框进行提醒，并停止纪录学习时长
      </div> -->
    </div>
    <div>
      <div v-if="state == 1" class="next">
        <el-button @click="$router.go(-1)" class="bt1" plain>取消</el-button>
        <el-button @click="go(2)" type="primary" class="bt1">下一步</el-button>
      </div>

      <div v-if="state == 3" class="next">
        <el-button @click="$router.go(-1)" class="bt1" plain>取消</el-button>
        <el-button @click="go(-1)" class="bt1">上一步</el-button>
        <el-button @click="saveCourse()" type="primary" class="bt1"
          >保存</el-button
        >
      </div>
    </div>
    <ScormPreview
      :visible.sync="previewVisible"
      :url="previewUrl"
    ></ScormPreview>
  </div>
</template>
<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import draggable from 'vuedraggable';
import ImgCropper from './ImgCropper.vue';
import dayjs from 'dayjs';
import ScormPreview from '@/components/scormPreview/IndexPage.vue';
import {
  listCourseType,
  listCourseware,
  saveCourse,
  getCourseDetail,
  uploadFile,
  listDefaultUrl
} from '@/api/CourseStore';
@Component({
  name: 'create',
  components: {
    ImgCropper,
    draggable,
    ScormPreview
  }
})
export default class Create extends Vue {
  private imgBox = false;
  private word = '';
  private hour = [
    { name: '0.5h', id: 0.5 },
    { name: '1h', id: 1 },
    { name: '1.5h', id: 1.5 },
    { name: '2h', id: 2 },
    { name: '2.5h', id: 2.5 },
    { name: '3h', id: 3 },
    { name: '3.5h', id: 3.5 },
    { name: '4h', id: 4 }
  ];
  private isStudyInOrder = 0;
  private preventCheatEnabled = 0;
  preventCheatTime = 0;
  private selectCourse = [
    // { coursewareName: 1, id: 1,  h:0,m:5,s:0},
    // { coursewareName: 2, id: 2,  studyDuration1: [0, 5, 0] },
    // { coursewareName: 3, id: 3,  studyDuration1: [0, 5, 0] }
  ];
  private allCourse = [
    // { coursewareName: 1, id: 1, modifiedTime: 0  },
    // { coursewareName: 2, id: 2, modifiedTime: 0 },
    // { coursewareName: 3, id: 3, modifiedTime: 0 },
    // { coursewareName: 4, id: 4, modifiedTime: 0 }
  ];
  private aaa = 0;
  private all = false;
  private multipleSelection = [];
  private coverUrl = '';
  private compDataArray = [];
  private ruleForm = {
    courseName: '',
    courseTypeId: '',
    courseHour: '',
    studyPurpose: ''
  };
  private rules = {
    courseName: [
      { required: true, message: '请输入课程名称', trigger: 'blur' },
      { min: 1, max: 30, message: '长度在30个字符内', trigger: 'blur' }
    ],
    courseTypeId: [
      { required: true, message: '请选择课程类型', trigger: 'change' }
    ],
    courseHour: [{ required: true, message: '请选择课时', trigger: 'change' }]
  };
  private state = 1;
  private cropperVisible = false;
  private imgInfo = {
    url: '',
    name: ''
  };
  private input(val) {
    console.log(val);
    console.log(this.$refs.i1[0].value);
  }
  private handleClose2() {
    this.all = false;
  }
  private chooseImg() {
    this.coverList.map((v) => {
      if (v.checked) {
        this.coverUrl = v.url;
      }
    });
    this.imgBox = false;
  }
  private uploadCover = async (file: File) => {
    this.coverUrl = '';
    console.log(this);
    try {
      const formData = new FormData();
      formData.append('file', file);
      formData.append('isConvertPdf', 'false');

      const { data } = await uploadFile(formData);
      this.onCoverChange(data.fileUrl);
      //this.coverUrl = data.fileUrl;
      console.log(data, 99999);
    } catch (e) {
      console.log(e);
    } finally {
      URL.revokeObjectURL(this.imgInfo.url);
    }
  };
  private onCoverChange(file) {
    this.coverUrl = file;
  }
  private hideCropperDialog() {
    this.cropperVisible = false;
  }
  private choose(i) {
    this.state = i;
  }
  private handleAvatarSuccess() {}
  private beforeUpload(file) {
    //console.log(file);
    if (!/\.(jpg|png|JPG|PNG|jpeg|JPEG)$/.test(file.name)) {
      this.$message.warning('封面图片格式只支持jpg,png,jpeg!');
      return false;
    }
    if (file.size > 1024 * 1024 * 10) {
      this.$message.warning('图片大小不能超过10MB!');
      return false;
    }

    this.imgInfo = {
      url: URL.createObjectURL(file),
      name: file.name
    };

    this.cropperVisible = true;
    return false;
  }
  private select = [];
  private checkSelectable(row) {
    // return !this.select.includes(row.id);
    let ff = false;
    this.selectCourse.map((v) => {
      if (v.id == row.id) {
        ff = true;
      }
    });
    return !ff;
  }
  private handleSelectionChange(val) {
    this.multipleSelection = val;
  }

  private chooseV(f) {
    //(this.$refs.all as any).clearSelection();
    this.all = f;
    this.select = [];
    this.selectCourse.map((v) => {
      this.select.push(v.id);
    });
    console.log(this.select);
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
  }
  private changeType() {
    this.selectCourse.map((v) => {
      this.allCourse.map((u) => {
        if (v.coursewareId == u.coursewareId) {
          u.type = 1;
        }
      });
    });
    console.log(this.allCourse, 99999);
  }
  private save() {
    //判断id,如果没有就加入
    //this.selectCourse = [];

    this.all = false;
    this.pageNo = 1;
    this.pageSize = 10;
    this.queryName = '';
    this.multipleSelection.map((v) => {
      // v.studyDuration1 = { h: 0, m: 5, s: 0 };
      v = Object.assign(v, { h: 0, m: 5, s: 0 });
      if (!this.select.includes(v.id)) {
        this.selectCourse.push(v);
      }
    });
    this.listCourseware();
  }
  private cancel() {
    this.all = false;
    this.pageNo = 1;
    this.pageSize = 10;
    this.queryName = '';
    this.listCourseware();
  }
  private deleteA(index) {
    this.selectCourse.splice(index, 1);
    console.log(this.selectCourse);
    //this.chooseV(false);
  }
  private mounted() {
    //this.changeType();
  }
  private go(v) {
    if (v == 2) {
      (this.$refs.ruleForm as any).validate((valid) => {
        if (valid) {
          this.state = 2;
        } else {
          this.state = 2;
          return false;
        }
      });
    }
    if (v == 3) {
      this.state = 3;
    }
    if (v == -1) {
      this.state = this.state + v;
    }
  }
  private formatSeconds(value) {
    let theTime = parseInt(value); // 需要转换的时间秒
    let theTime1 = 0; // 分
    let theTime2 = 0; // 小时
    let theTime3 = 0; // 天
    if (theTime > 60 && theTime != 3600) {
      theTime1 = parseInt((theTime / 60) as any);
      theTime = parseInt((theTime % 60) as any);
      if (theTime1 > 60) {
        theTime2 = parseInt((theTime1 / 60) as any);
        theTime1 = parseInt((theTime1 % 60) as any);
        if (theTime2 > 24) {
          //大于24小时
          theTime3 = parseInt((theTime2 / 24) as any);
          theTime2 = parseInt((theTime2 % 24) as any);
        }
      }
    } else if (theTime == 60) {
      theTime = 0;
      theTime1 = 1;
      theTime2 = 0;
    } else if (theTime == 3600) {
      theTime = 0;
      theTime1 = 0;
      theTime2 = 1;
    }
    const r = { h: 0, m: 0, s: 0 };
    r.h = theTime2;
    r.m = theTime1;
    r.s = theTime;

    console.log(r);

    return r;
  }
  private datadragEnd(e) {
    e.preventDefault();
    console.log(this.selectCourse);
  }
  private courseType = [];
  private listCourseType() {
    listCourseType().then((res) => {
      this.courseType = res.data;
    });
  }
  private created() {
    this.listCourseType();
    this.listCourseware();
    this.getCourseDetail();
    this.listDefaultUrl();
  }
  private pageNo = 1;
  private pageSize = 10;
  private queryName = '';
  private total = 0;
  //获取课件
  private async listCourseware() {
    //this.allCourse = [];
    const params = {
      pageNo: this.pageNo,
      pageSize: this.pageSize,
      queryName: this.queryName
    };
    await listCourseware(params).then((res) => {
      this.allCourse = res.data.records;

      this.total = res.data.total;
      //this.chooseV(true);
    });
  }
  private saveCourse() {
    const data = {
      courseCoursewareRelationList: [],
      courseHour: '',
      courseName: '',
      courseTypeId: '',
      coverUrl: '',
      id: this.$route.query.id || '',
      isStudyInOrder: 0,
      preventCheatEnabled: 0,
      preventCheatTime: 0,
      studyPurpose: ''
    };
    this.selectCourse.map((v, index) => {
      const studyDuration = v.h * 3600 + v.m * 60 + v.s * 1;
      const coursewareId = v.id;
      const coursewareOrder = index;
      const coursewareName = v.coursewareName;
      data.courseCoursewareRelationList.push({
        studyDuration,
        coursewareId,
        coursewareOrder,
        coursewareName
      });
    });
    data.courseHour = this.ruleForm.courseHour;
    data.courseName = this.ruleForm.courseName;
    data.courseTypeId = this.ruleForm.courseTypeId;
    data.coverUrl = this.coverUrl;
    data.isStudyInOrder = this.isStudyInOrder * 1;
    data.preventCheatEnabled = this.preventCheatEnabled * 1;
    data.preventCheatTime = this.preventCheatTime * 1;
    data.studyPurpose = this.ruleForm.studyPurpose;
    if (!data.courseName) {
      this.$message.error('课程名称不能为空！');
      this.state = 1;
      return;
    }
    if (!data.courseTypeId) {
      this.$message.error('课程类型不能为空！');
      this.state = 1;
      return;
    }
    if (!data.courseHour) {
      this.$message.error('课时不能为空！');
      this.state = 1;
      return;
    }
    if (data.courseCoursewareRelationList.length == 0) {
      this.$message.error('请选择课件！');
      this.state = 2;
      return;
    }

    const ao = this.selectCourse.filter((v) => {
      if (v.h > 23 || v.m > 59 || v.s > 59) {
        return v;
      }
      if (v.h == 0 && v.m == 0 && v.s == 0) {
        return v;
      }
    });
    if (ao.length != 0) {
      this.$message.error('小时数不能超过23!分钟数不能超过59!秒数不能超过59!');
      this.state = 2;
      return;
    }
    if (data.preventCheatEnabled == 1) {
      if (data.preventCheatTime > 10 || data.preventCheatTime == 0) {
        this.$message.error('防挂机时长最小1分钟，最大10分钟！');
        return;
      }
    } else {
      data.preventCheatTime = 0;
    }
    this.$loading['show']();
    saveCourse(data).then((res) => {
      if (res.code == 1000) {
        this.$loading['hide']();
        this.$message.success('操作成功！');
        this.$router.go(-1);
      }
    });
    console.log(data);
  }

  private async handleSizeChange(val) {
    this.pageNo = 1;
    this.pageSize = val;
    await this.listCourseware();
    //this.chooseV(true);
    this.allCourse.map((v, index) => {
      if (this.select.includes(v.id)) {
        (this.$refs.all as any).toggleRowSelection(this.allCourse[index], true);
      }
    });
  }
  private async handleCurrentChange(val) {
    this.pageNo = val;
    await this.listCourseware();
    //this.chooseV(true);
    this.allCourse.map((v, index) => {
      if (this.select.includes(v.id)) {
        (this.$refs.all as any).toggleRowSelection(this.allCourse[index], true);
      }
    });
  }
  private getID(row) {
    return row.id; //这个id需要换成自己所绑定的Key
  }
  //编辑
  getCourseDetail() {
    if (this.$route.query.id) {
      //this.formatSeconds(300);
      getCourseDetail({ id: this.$route.query.id }).then((res) => {
        this.selectCourse = [];
        this.selectCourse.length = res.data.courseCourseWareInfoList.length;
        res.data.courseCourseWareInfoList.map((v) => {
          //v.studyDuration1 = this.formatSeconds(v.studyDuration);
          v = Object.assign(v, this.formatSeconds(v.studyDuration));
          v.id = v.coursewareId;
          this.selectCourse[v.coursewareOrder] = v;
        });
        console.log(this.selectCourse, 999999);
        this.ruleForm.courseName = res.data.courseName;
        this.ruleForm.courseHour = res.data.courseHour;
        this.ruleForm.courseTypeId = res.data.courseTypeId;
        this.ruleForm.studyPurpose = res.data.studyPurpose;
        this.coverUrl = res.data.coverUrl;
        this.isStudyInOrder = res.data.isStudyInOrder;
        this.preventCheatEnabled = res.data.preventCheatEnabled;
        this.preventCheatTime = res.data.preventCheatTime;
      });
    }
  }
  private searchK() {
    this.pageNo = 1;
    this.pageSize = 10;
    this.listCourseware();
  }
  private coverList = [];
  private listDefaultUrl() {
    listDefaultUrl().then((res) => {
      const data = {
        url: '',
        checked: false
      };
      res.data.map((v, index) => {
        if (index == 0) {
          data.checked = true;
        }
        data.url = v;
        this.coverList.push(data);
      });
      if (!this.$route.query.id) {
        this.coverUrl = this.coverList[0].url;
      }
    });
  }
  private time(val) {
    return dayjs(val).format('YYYY-MM-DD HH:mm:ss');
  }
  previewVisible = false;
  previewUrl = '';
  openPreview(row) {
    console.log(row);
    this.previewVisible = true;
    this.previewUrl = row.fileUrl;
  }
}
</script>
<style scoped lang="scss">
.content {
  position: absolute;
  width: 100%;
  min-height: 100%;
  overflow: auto;
  .d1 {
    height: 40px;

    display: flex;
    margin-bottom: 16px;
  }
  .bt {
    height: 40px;
    width: 300px;
    cursor: pointer;
    background: white;
    margin-right: 6px;
    text-align: center;
    line-height: 40px;
    vertical-align: baseline;
    color: rgb(0, 78, 162);
    position: relative;
    .i {
      position: absolute;
      left: 48%;
      top: 100%;
      height: 8px;
      width: 25px;
      background: url('../../../assets/three.svg') no-repeat center;
    }
  }
  .state {
    background: rgb(0, 78, 162);
    color: white;
  }
  .d2 {
    position: absolute;
    width: 98%;
    height: 80%;
    display: flex;
    padding: 20px;
    background: #fff;
    .d21 {
      width: 80%;
    }
  }
  .d3 {
    position: absolute;
    width: 98%;
    max-height: 85%;
    overflow: auto;
    background: #fff;
    padding: 20px;
  }
  .d3c {
    position: absolute;
    width: 98%;

    height: 80%;
    background: #fff;
    padding: 20px;
  }
  .foot {
    height: 50px;
  }
}
.span {
  display: inline-block;
  width: 100px;
  .input {
    width: 60px;
    margin-right: 10px;
  }
}
.mixed-training-create-container {
  min-height: 100vh;
  overflow: scroll;
  display: flex;
  flex-direction: column;
  .task-container {
    flex: 1;
    background-color: #fff;
    // margin-left: 26px;
    font-size: 16px;
    .th {
      display: flex;
      align-items: center;
      color: #333;
      border-radius: 5px;
      margin-top: 16px;
      margin-top: 0;
      background-color: #f6f6f6;
      .type {
        width: 108px;
        display: flex;
        align-items: center;
        svg {
          font-size: 26px;
          margin-right: 10px;
        }
      }
      .img {
        width: 512px;
        display: flex;
        padding-left: 10px;
      }
      .switch {
        width: 634px;
        display: flex;
        overflow: hidden;
        text-overflow: ellipsis;
        word-wrap: break;
      }
      .status {
        width: 160px;
        display: flex;
      }
      .operate {
        width: 10%;
        color: #333;
        i {
          cursor: pointer;
          user-select: none;
          & ~ i {
            margin-left: 20px;
          }
        }
      }
    }
    .items {
      .th {
        display: flex;
        align-items: center;
        color: #333;
        border-radius: 5px;
        margin-top: 16px;
        margin-top: 0;
        background-color: #f6f6f6;
        .type {
          width: 108px;
          display: flex;
          align-items: center;
          svg {
            font-size: 26px;
            margin-right: 10px;
          }
        }
        .img {
          width: 512px;
          display: flex;
          padding-left: 10px;
        }
        .switch {
          width: 634px;
          display: flex;
        }
        .status {
          width: 160px;
          display: flex;
        }
        .operate {
          width: 10%;
          color: #333;
          i {
            cursor: pointer;
            user-select: none;
            & ~ i {
              margin-left: 20px;
            }
          }
        }
      }
    }
  }
}
.containerC {
  min-height: 70vh;
  overflow: auto;
  .th {
    display: flex;
    justify-content: space-between;
    .type {
      flex: 0 1 108px;
      text-align: center;
    }
    .img {
      flex: 0 1 512px;
      padding-left: 10px;
    }
    .switch {
      flex: 0 1 550px;
    }
    .status {
      flex: 0 1 100px;
    }
    .operate {
      flex: 0 1 200px;
      padding-right: 100px;
    }
  }
}
.header {
  background: rgb(246, 246, 246);
  height: 50px;
  line-height: 50px;
  margin-top: 20px;
  border: 1px solid #ddd;
}
.list {
  height: 60px;
  line-height: 60px;
}
.p {
  display: block;
  width: 300px;
  height: auto;
  word-wrap: break-word;
  word-break: break-all;
  overflow: hidden;
  line-height: 20px;
  top: 50%; /*偏移*/
  transform: translateY(-50%);
  position: relative;
}
.questionBox {
  border: 1px solid $color-gray-border;
  border-top: none;
  position: relative;
  height: 64px;
  align-items: center;
}

.questionBox:hover {
  border: 1px solid $color-primary;
}
.imgBox {
  display: flex;
  height: 300px;
  justify-content: space-between;
  flex-flow: wrap;
  div {
    width: 30%;
    height: 200px;
    border: 1px solid #ddd;
  }
  .checked {
    border: 2px solid rgb(0, 78, 162);
  }
}
.next {
  position: absolute;
  bottom: 10%;
  background: white;
  width: 100%;
  text-align: center;
  height: 150px;
  line-height: 150px;
  .bt1 {
    width: 110px;
    height: 34px;
    margin-right: 40px;
  }
}
.middle {
  height: 82%;
  overflow: auto;
}
.foot {
  margin-top: 10px;
  width: 100%;
  height: 80px;
  position: absolute;
  bottom: 0;
  border-top: 1px solid #e6e9edff;
  text-align: center;
  .bt1 {
    margin-top: 10px;
    width: 110px;
    height: 34px;
    margin-right: 40px;
  }
}
.title {
  height: 40px;
  font-size: 16px;
  font-weight: 500;
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
.nodata {
  width: 100%;
  border: 1px solid #ddd;
  border-top: none;
  height: 100px;
  line-height: 100px;
  text-align: center;
  margin: 0;
  margin-bottom: 21%;
}
.for {
  height: 40px;
  line-height: 40px;
  padding-left: 10px;
  .span {
    display: inline-block;
    width: 80px;
    text-align: left;
  }
  .word {
    margin-left: 10px;
    color: #94979aff;
  }
}
.bt1 {
  margin-top: 10px;
  width: 110px;
  height: 34px;
  margin-right: 40px;
}
.num {
  width: 18px;
  height: 18px;
  line-height: 18px;
  background: #0000000f;
  border-radius: 50%;
  margin-right: 5px;
  display: inline-block;
  vertical-align: middle;
  color: #004ea2ff;
}
.numCheck {
  background: #ffffffff;
  color: #004ea2ff;
  width: 18px;
  height: 18px;
  line-height: 18px;

  border-radius: 50%;
  margin-right: 5px;
  display: inline-block;
  vertical-align: middle;
}
</style>
