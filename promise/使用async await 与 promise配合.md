## 使用async await 与 promise配合

```
  // 群组名称失焦
    nameBlur: async function(name) {
      var isExist = await this.checkGroup(name);
      if (isExist) {
        var that = this;
        this.$confirm("已有相同名称的组，是否继续？", "提示", {
          confirmButtonText: "确定",
          cancelButtonText: "取消",
          type: "warning"
        })
          .then(() => {
            that.form.name = name;
          })
          .catch(() => {
            that.form.name = "";
            this.$message({
              type: "info",
              message: "已取消"
            });
          });
      }
    },
    // 群组号码失焦
    phoneBlur: async function(phone) {
      var isExist = await this.checkGroup(phone);
      if (isExist) {
        var that = this;
        that.$notify.error({
          title: "错误",
          message: "组号码不能重复！"
        });
        that.form.phone = "";
      }
    },
    // 查询群组是否存在
    checkGroup(keyword) {
      var data = {
        pageNumber: 1,
        pageSize: 600,
        keyword: keyword
      }; //获取群组所有组
      return new Promise(resolve => {
        getGroupList(data).then(response => {
          console.log(response);
          if (response.data.code == 0) {
            let result = response.data.data;
            let total = result.total;
            if (total == 0) {
              var isExist = false;
              //没有数据
            } else {
              var isExist = true;
            }
            resolve(isExist);
          }
        });
      }).catch(err => {
        let isExist = false;
        //没有数据
        return isExist;
      });
    },
```
