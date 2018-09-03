<template>
  <div class="row form-group">
                <label class="col-md-2 control-label word-spacing-25">配 图</label>
                <div class="col-md-10 padding-0">
                    <div class="col-md-12 ">
                        <label class="btn btn-primary" for="file2" style="float:left">选择文件</label>
                        <input id="file2" style="display: none" type="file" @change="filebox"/>
                    </div>
                    <div class="col-md-12">
                        <img alt="配图预览" class="img-responsive" style="float:left;width:200px;height:200px" :src="imgurl" v-if="imgurl !==''&&file==''">
                        <img alt="配图预览" class="img-responsive" style="float:left;width:200px;height:200px" :src="Src" v-if='file !==""'>
                    </div>
                    <div class="col-md-12">
                        <div class="table-responsive col-md-8 padding-0">
                            <table class="table">
                                <thead>
                                <tr><th>图片名</th>
                                <th>文件大小</th>
                                <th>进度</th>
                                <th>操作</th>
                                <th>操作</th>
                                </tr></thead>
                                <thead v-if="this.file">
                                <tr><th v-if="Src !==''">{{name}}</th>
                                <th v-if="Src !==''">{{size}}</th>
                                <th v-if="this.file">
                                    <div class="progress">
                                        <div class="progress-bar" role="progressbar" aria-valuenow="60" aria-valuemin="0" aria-valuemax="100" :style='`width:${width}%`'>
                                            <span class="sr-only"></span>
                                        </div>
                                    </div>
                                </th>
                                <th v-if="this.file"><span v-if='width===100' class='glyphicon glyphicon-ok'></span></th>
                                <th v-if="this.file">
                                    <button type="button" class='btn btn-success'  @click='upload'>上传</button>
                                    <button type="button" class='btn btn-danger' @click='deleteImg'>删除</button>
                                </th>
                                </tr></thead>
                            </table>
                        </div>
                    </div>
                </div>
            </div>
</template>

<script>
import Ajaxservice from "@/ajax/ajax-service";
export default {
  data() {
    return {
      file: "",
      name: "",
      size: "",
      progress: "",
      Src: "",
      url: "",
      width: 0
    };
  },
  created() {},
  watch: {
    imgurl: (newV, oldV) => {
      if (newV) {
        this.Src = this.newV;
      }
    }
  },
  props: ["imgurl"],
  methods: {
    deleteImg() {
      this.Src = "";
      this.$emit("deleteUrl");
    },
    filebox() {
      let that = this;
      this.file = document.getElementById("file2").files[0];
      this.name = this.file.name.split(".")[0];
      this.size = (this.file.size / 1024 / 1024).toFixed(2) + "M";
      let reader = new FileReader();
      reader.readAsDataURL(this.file);
      reader.onload = function(e) {
        that.Src = this.result;
      };
    },
    int() {
      setInterval(() => {
        if (this.width <= 80) {
          this.width += 0.5;
        }
      }, 1);
    },
    upload() {
      let formdata = new FormData();
      this.int();
      formdata.append("file", this.file);
      Ajaxservice.imgupload(formdata)
        .then(res => {
          if (res.data.code === 0) {
            this.url = res.data.data.url;
            this.$emit("load", this.url);
            clearInterval(this.int);
            this.width = 100;
          }
        })
        .catch(res => {
          console.log(res);
        });
    }
  }
};
</script>

<style scoped>
.progress {
  margin: 0;
}
</style>



// WEBPACK FOOTER //
// src/components/fileupload.vue