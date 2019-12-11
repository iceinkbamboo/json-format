<template>
  <div class="box">
    <div class="box-left">
      <div class="box-title">JSON字符串输入</div>
      <el-input
        type="textarea"
        placeholder="请输入内容"
        v-model="textarea"
        :rows="40"
        @input="inputChange"
      >
      </el-input>
    </div>
    <div class="box-right">
      <div class="box-title">JSON格式转化</div>
      <pre v-html="syntaxHighlight(textJosn)" @click="clickDom($event)"></pre>
    </div>
  </div>
</template>

<script>
export default {
  name: "home",
  components: {},
  data() {
    return {
      textarea: "",
      textJosn: ""
    };
  },
  methods: {
    inputChange() {
      let textarea = this.textarea;
      try {
        let textJosn = JSON.parse(textarea);
        this.textJosn = textJosn;
      } catch (e) {
        this.textJosn = "This is not right JSON";
      }
    },
    // JSON格式转化
    syntaxHighlight(json) {
      if (typeof json != "string") {
        json = JSON.stringify(json, undefined, 2);
      }
      json = json
        .replace(/&/g, "&")
        .replace(/</g, "<")
        .replace(/>/g, ">");
      return json.replace(
        /("(\\u[a-zA-Z0-9]{4}|\\[^u]|[^\\"])*"(\s*:)?|\b(true|false|null)\b|-?\d+(?:\.\d*)?(?:[eE][+-]?\d+)?|{|}|\[|\])/g,
        function(match) {
          console.log(match);
          var cls = "number";
          if (/^"/.test(match)) {
            if (/:$/.test(match)) {
              cls = "key";
            } else {
              cls = "string";
            }
          } else if (/true|false/.test(match)) {
            cls = "boolean";
          } else if (/null/.test(match)) {
            cls = "null";
          } else if (/{/.test(match)) {
            return '<i class="open"></i><span class="Object{...}">' + match;
          } else if (/}/.test(match)) {
            return match + "</span>";
          } else if (/\[/.test(match)) {
            return '<i class="open"></i><span class="Array">' + match;
          } else if (/\]/.test(match)) {
            return match + "</span>";
          }
          return '<span class="' + cls + '">' + match + "</span>";
        }
      );
    },
    clickDom(e) {
      let target = e.target;
      if (target.nodeName == "I") {
        if (target.className == "open") {
          target.className = "close";
          let dom = e.target.nextSibling;
          dom.style.display = "none";
          var childNode = document.createElement("em");
          if (dom.className == "Array") {
            let childrenList = dom.children;
            let newList = [];
            for (let i = 0; i < childrenList.length; i++) {
              if (childrenList[i].nodeName == "SPAN") {
                newList.push(childrenList[i]);
              }
            }
            childNode.innerHTML = `${dom.className}[<em style="color:#25aae2;">${newList.length}</em>]`;
          } else {
            childNode.innerHTML = dom.className;
          }
          e.target.parentNode.insertBefore(childNode, e.target.nextSibling);
        } else if (target.className == "close") {
          e.target.parentNode.removeChild(e.target.nextSibling);
          target.className = "open";
          let dom = e.target.nextSibling;
          dom.style.display = "";
        }
      }
    }
  }
};
</script>

<style lang="less" scoped>
.box {
  width: 100%;
  height: 100%;
  text-align: left;

  &-title {
    width: 100%;
    height: 40px;
    line-height: 40px;
    font-size: 16px;
    color: blue;
    border-bottom: 1px solid #cccccc;
    text-indent: 20px;
  }

  &-left {
    width: 50%;
    height: 100%;
    float: left;
  }

  &-right {
    width: 50%;
    height: 100%;
    float: right;
    box-sizing: border-box;
    border: 1px solid #cccccc;
    text-align: left;
  }
}
/deep/ textarea {
  height: 100% !important;
}
pre {
  margin: 0;
  font-weight: 900;
  font-size: 14px;
}
/deep/.string {
  color: #3ab54a;
}
/deep/.number {
  color: #25aae2;
}
/deep/.boolean {
  color: #f98280;
}
/deep/.null {
  color: #f1592a;
}
/deep/.key {
  color: #92278f;
}
/deep/.open {
  display: inline-block;
  width: 12px;
  height: 12px;
  background: url("../assets/close.png") no-repeat;
  background-size: 100% 100%;
  vertical-align: middle;
  cursor: pointer;
}
/deep/.close {
  display: inline-block;
  width: 12px;
  height: 12px;
  background: url("../assets/open.png") no-repeat;
  background-size: 100% 100%;
  vertical-align: middle;
  cursor: pointer;
}
</style>
