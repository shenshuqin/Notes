# TODoList

html

```js


<h2>todoList</h2>
<div class="todolist">
    <input class="form_input" type="text" [(ngModel)]="keyword" (keyup)="doAdd($event)" />

    <hr>


    <h3>待办事项</h3>
    
    <ul>
      <li *ngFor="let item of todolist;let key=index;" [hidden]="item.status==1">
        
        
       <input type="checkbox" [(ngModel)]="item.status" />  {{item.title}}   ------ <button (click)="deleteData(key)">X</button>
      
      
      
      </li>
    </ul>


    <h3>已完成事项</h3>



    <ul>
        <li *ngFor="let item of todolist;let key=index;" [hidden]="item.status==0">
          
          
         <input type="checkbox" [(ngModel)]="item.status" />  {{item.title}}   ------ <button (click)="deleteData(key)">X</button>
        
        
        
        </li>
      </ul>

</div>
```

ts

```ts
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-todolist',
  templateUrl: './todolist.component.html',
  styleUrls: ['./todolist.component.scss']
})
export class TodolistComponent implements OnInit {


  public keyword:string;

  public todolist:any[]=[];


  constructor() { }

  ngOnInit() {
  }

  doAdd(e){

    
    if(e.keyCode==13){
       
        
        if(!this.todolistHasKeyword(this.todolist,this.keyword)){
          this.todolist.push({
            title:this.keyword,
            status:0                   //0表示代办事项  1表示已完成事项
          });

          this.keyword='';

        }else{

          alert('数据已经存在');
          this.keyword='';
        }
     }
    
  }

  deleteData(key){

    this.todolist.splice(key,1);
  }

  //如果数组里面有keyword返回true  否则返回false
  todolistHasKeyword(todolist:any,keyword:any){

   

    //异步  会存在问题
    // todolist.forEach(value => {

    //   if(value.title==keyword){

    //       return true;
    //   } 
    // });

    

    if(!keyword) return false;

    for(var i=0;i<todolist.length;i++){
      if(todolist[i].title==keyword){

          return true;
      } 
    }
    return false;

  }


}

```

