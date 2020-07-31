## json-serson的使用:

简书教程:https://www.jianshu.com/p/87f9829dc516

+ 创建一个json文件
+ 安装 `npm install -g json-server`
+ 运行 `json-server --watch --port 53000 db.json`
+ 使用
```html
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>

</head>
<script src="jquery.js"></script>
<body>
<p id="s"></p>
</body>
<script>
    $(function(){
        $.ajax({
            type: "GET",
            url: " http://localhost:53000/huwei",
            // data: {username:$("#username").val(), content:$("#content").val()},
            dataType: "json",
            success: function (data) {
                //     $('#resText').empty();   //清空resText里面的所有内容
                //     var html = '';
                //     $.each(data, function(commentIndex, comment){
                //         html += '<div class="comment"><h6>' + comment['username']
                //             + ':</h6><p class="para"' + comment['content']
                //             + '</p></div>';
                //     });
                //     $('#resText').html(html);
                // }
                // });
                console.log(data);
                $("#s").html(data)
            }
        })
    })
    </script>
</html>
```