### Form

1. render内获取form中的getFieldDecorator

   ```html
   const { getFieldDecorator } = this.props.form;
   ```

2. 使用getFieldDecrator将input组件值绑定到一个name上

   ```html
   <FormItem>
       {getFieldDecorator('name', {
       rules: [{ required: true, message: 'Please input your name!' }],
       })(
       <Input placeholder="标题"/>
       )}
   </FormItem>
   ```

3. 指定form的onSubmit回调方法

   ```html
   <Form onSubmit={this.handleSubmit}>
   ```

4. 通过回调方法中的事件对象获取表单内容进行操作

   ```javascript
   handleSubmit = (e) => {
   e.preventDefault();
   this.props.form.validateFields((err, values) => {
   if (!err) {
   //进行一些操作
   console.log('Received values of form: ', values);
   }
   });
   }
   ```

5. 将类包装并添加到dom中

   ```javascript
   const WrappedIndex = Form.create()(Index);
   ReactDom.render(<div><WrappedIndex></WrappedIndex></div>, document.querySelector("#content"));
   ```

   ​

