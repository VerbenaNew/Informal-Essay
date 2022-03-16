# Element el-form 表单非必填验证
### 在项目中经常会遇到表单数据非必填项，但是需要验证格式的情况，比如经纬度、电话号码或者邮编。

### 实现效果
当值不填写的时候，可以直接提交，填写的时候，验证格式即可。

### 代码实现
##### 1、rules定义验证规则，在form-item中一定要加上prop，不然会没有效果。


```
    <el-form
        :model="addDevice"
        label-width="120px"
        label-position="right"
        :rules="addDevRule"
        ref="formAdd"
        size="small"
      >
        <el-form-item label="经度" prop="longitude" collapse-tags>
          <el-input
            v-model="addDevice.longitude"
            placeholder="请输入所属域下坐标，范围-180~180"
          ></el-input>
        </el-form-item>
        <el-form-item label="纬度" prop="latitude" collapse-tags>
          <el-input
            v-model="addDevice.latitude"
            placeholder="请输入所属域下坐标，范围-90~90"
          ></el-input>
        </el-form-item>
  
        </el-form-item>
    </el-form>
```

##### 2、定义验证规则：输入框有值时进行验证，无值则callback。注意：无值一定要返回callback()，否则便提交不了。


```
    const validlatitude = (rule, value, callback) => {
      if (value && value != "") {
        if (!validcodelatitude(value)) {
          return callback(new Error("输入格式不合法！"));
        } else {
          callback();
        }
      } else {
        callback();
      }
    };
    const validlongitude = (rule, value, callback) => {
      if (value && value != "") {
        if (!validcodelongitude(value)) {
          return callback(new Error("输入格式不合法！"));
        } else {
          callback();
        }
      } else {
        callback();
      }
    };
```

##### 3、rules中required: false，validator引入自定义的验证validlatitude。


```
    addDevRule: {
        latitude: [
          {
            required: false,
            validator: validlatitude,
            trigger: "blur",
            message: "输入合法纬度"
          }
        ],
        longitude: [
          {
            required: false,
            validator: validlongitude,
            trigger: "blur",
            message: "输入合法经度"
          }
        ],
      },
```
