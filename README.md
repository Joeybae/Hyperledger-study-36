# Hyperledger-study-36

하이퍼레져 앱 만들기 실습 36일차 - 미들웨어 설치, 사용, 추가

출처 : https://opentutorials.org/course/3370/21387

참조 : https://expressjs.com/

1. 미들웨어 설치 - body-parser

        # npm install body-parser —save

        # app.use(bodyParser.urlencoded({extended: false}))

2. 미들웨어 만들기

        # npm install compression --save

main.js

        app.use(compression());
        app.get('*', function(request, response, next){
          fs.readdir('./data', function(error, filelist){
            request.list = filelist;
            next();
          });
        });

3. 미들웨어 사용순서

main.js

        app.get('/user/:id', function (req, res, next) {
          console.log('ID:', req.params.id);
          next();
        }, function (req, res, next) {
          res.send('User Info');
        });

        // handler for the /user/:id path, which prints the user ID
        app.get('/user/:id', function (req, res, next) {
          res.end(req.params.id);
        });

4. 이미지 파일 추가

정적인 파일 추가하도록 허용

        app.use(express.static('public'));

        <img src="/images/hello.jpg" style="width:300px; display:block; margin:10px;">

5. 에러처리

1) 미들웨어 추가

        app.use(function(req, res, next) {
          res.status(404).send('Sorry cant find that!');
        });

2) 에러 메세지 출력

        app.get('/page/:pageId', function(request, response) { 
          var filteredId = path.parse(request.params.pageId).base;
          fs.readFile(`data/${filteredId}`, 'utf8', function(err, description){
            if(err){
              next(err);
            } else {
              var title = request.params.pageId;
              var sanitizedTitle = sanitizeHtml(title);
              var sanitizedDescription = sanitizeHtml(description, {
                allowedTags:['h1']
              });
              var list = template.list(request.list);
              var html = template.HTML(sanitizedTitle, list,
                `<h2>${sanitizedTitle}</h2>${sanitizedDescription}`,
                ` <a href="/create">create</a>
                  <a href="/update/${sanitizedTitle}">update</a>
                  <form action="/delete_process" method="post">
                    <input type="hidden" name="id" value="${sanitizedTitle}">
                    <input type="submit" value="delete">
                  </form>`
              );
              response.send(html);
            }
          });
        });

3) 에러 시 출력되는 메세지 변경

        app.use(function(err, req, res, next) {
          console.error(err.stack);
          res.status(500).send('Something broke!');
        });
