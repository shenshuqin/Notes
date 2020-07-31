## 创建服务

```shell
ng g service my-new-service
//创建到指定目录
ng g service services/storage
```

## 使用

1.app.modules.ts中引入创建服务

```js
//引入并且配置服务
import { StorageService } from './services/storage.service';
```

2.NgModule里面的prividers里面依赖注入服务

```js
@NgModule({
  declarations: [
    AppComponent,
    SearchComponent,
    TodolistComponent   
  ],
  imports: [
    BrowserModule,
    FormsModule
  ],
  providers: [StorageService],
  bootstrap: [AppComponent]
})
```

3.在需要使用的页面引入服务

```js
//引入并且配置服务
import { StorageService } from './services/storage.service';
```

```js
   constructor(public storage:StorageService) { 

          let s=this.storage.get();

          console.log(s);
      }
```

### 案列

storage.service.ts

```js
import { Injectable } from '@angular/core';

@Injectable({
  providedIn: 'root'
})
export class ServerService {

  constructor() { }
    set(key:string,value:any){
        localStroage.setItem(key,JSON.stringfy(value))
    }
    get(){
        return JSON.parse(loacalStorage.getItem(key))
    }
    remove(key:string) {
        localStorage.removeItem(key)
    }
}

```

使用

```html
import { Component, OnInit } from '@angular/core';
//引入服务
import { StorageService } from '../../services/storage.service';

@Component({
  selector: 'app-todolist',
  templateUrl: './todolist.component.html',
  styleUrls: ['./todolist.component.scss']
})
export class TodolistComponent implements OnInit {


  public keyword:string;

  public todolist:any[]=[];


  constructor(public storage:StorageService) {

   }

  ngOnInit() {

    //页面刷新会调用这个生命周期
    var todolist:any=this.storage.get('todolist');

    if(todolist){
      this.todolist=todolist;        
    }
  }

  doAdd(e){

    
    if(e.keyCode==13){
       
        
        if(!this.todolistHasKeyword(this.todolist,this.keyword)){
          this.todolist.push({
            title:this.keyword,
            status:0                   //0表示代办事项  1表示已完成事项
          });

          this.keyword='';

          this.storage.set('todolist',this.todolist);          //用到this一定要注意this指向


        }else{

          alert('数据已经存在');
          this.keyword='';
        }
     }
    
  }

  deleteData(key){

    this.todolist.splice(key,1);

    this.storage.set('todolist',this.todolist); 
  }

    if(!keyword) return false;

    for(var i=0;i<todolist.length;i++){
      if(todolist[i].title==keyword){

          return true;
      } 
    }
    return false;

  }


  checkboxChage(){


    console.log('事件触发了');

    this.storage.set('todolist',this.todolist); 
  }

}

```

