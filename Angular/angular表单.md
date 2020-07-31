# 表单

## 表单事件

```js
<input type="text" (keydown)="keyDown($event)" />
```

```js
   keyDown(e){
    if(e.keyCode==13){
        console.log('按了一下回车')
    }else{


      //e.target 获取dom对象
      console.log(e.target.value);
    }
  
  }
```

### 双向绑定

```html
<h1>双休数据绑定--MVVM 只是针对表单</h1>


            <input type="text" [(ngModel)]='keywords' />

            {{keywords}}

            <br>
            <br>


            <button (click)="changeKeywords()">改变keywords的值</button>

            <br>
            <br>

            <button (click)="getKeywords()">获取keywords的值</button>
    
```

```js
  changeKeywords(){

    this.keywords='我是改变后的值';
  }

  getKeywords(){

    console.log(this.keywords)
  }
}
```

### 表单案列

```html
<h2>人员登记系统</h2>



<div class="people_list">
  <ul>
    <li>姓 名：<input type="text" id="username" [(ngModel)]="peopleInfo.username" value="fonm_input" /></li>


    <li>性 别：
      

      <input type="radio" value="1" name="sex" id="sex1" [(ngModel)]="peopleInfo.sex"> <label for="sex1">男 </label>　　　
      <input type="radio" value="2" name="sex"  id="sex2" [(ngModel)]="peopleInfo.sex"> <label for="sex2">女 </label>


    </li>

   <li>
     
    城 市：
      <select name="city" id="city" [(ngModel)]="peopleInfo.city">


          <option [value]="item" *ngFor="let item of peopleInfo.cityList">{{item}}</option>
      </select>



    </li>

    <li>
     
        爱 好：
         
        
        <span *ngFor="let item of peopleInfo.hobby;let key=index;">
            <input type="checkbox"  [id]="'check'+key" [(ngModel)]="item.checked"/> <label [for]="'check'+key"> {{item.title}}</label>

            &nbsp;&nbsp;
        </span>
    
    
    
     </li>

     <li>
     
       备 注：
         
        

       <textarea name="mark" id="mark" cols="30" rows="10" [(ngModel)]="peopleInfo.mark"></textarea>
    
    
    
     </li>
  </ul>



  <button (click)="doSubmit()" class="submit">获取表单的内容</button>


  <br>
  <br>
  <br>
  <br>



  <pre>


    {{peopleInfo | json}}
  </pre>


</div>
```

js

```js
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-form',
  templateUrl: './form.component.html',
  styleUrls: ['./form.component.scss']
})
export class FormComponent implements OnInit {


  public peopleInfo:any={

    username:'',
    sex:'2',
    cityList:['北京','上海','深圳'],
    city:'上海',

    hobby:[{

          title:'吃饭',
          checked:false
      },{

            title:'睡觉',
            checked:false
        },{

          title:'敲代码',
          checked:true
      }],

      mark:''

  }

  constructor() { }

  ngOnInit() {
  }

  doSubmit(){

    /*    
    jquery  dom操作

      <input type="text" id="username" />
      let usernameDom:any=document.getElementById('username');
      console.log(usernameDom.value);    
    */


    console.log(this.peopleInfo);


  }

}

```

