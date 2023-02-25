## - 개요
nodejs개발 환경에서 시퀄 라이즈(Sequelize)에 대해 알아보겠습니다.

시퀄 라이즈(Sequelize)란 무엇일까요? 시퀄 라이즈는 DB 작업을 쉽게 할 수 있도록 도와주는 ORM 라이브러리입니다.

ORM이란 무엇일까요? ORM(Object-Relational Mapping)에 약자입니다.

 

즉, ORM은 자바스크립트 객체와 관계형 데이터베이스를 서로 연결해주는 도구입니다.

## - 설치
 - 프로젝트 디렉터리를 만들고 이동
```
mkdir node-sequelize
cd node-sequelize
```
 - sequelize와 sequelize-cli 그리고 mssql를 설치해주세요. ([SQL Server docker image(1.5GB)](https://learn.microsoft.com/en-us/sql/linux/quickstart-install-connect-docker?view=sql-server-linux-ver15&preserve-view=true&pivots=cs1-bash#pullandrun2019)를 내려받아 사용)
```
npm install sequelize sequelize-cli tedious
```
 - 설치를 완료 후 sequelize init 명령어를 실행합니다.
```
sequelize init
```
해당 명령어를 실행하면 아래와 같이 디렉터리와 파일이 생성이 됩니다.

 - config

>>기본은 mysql로 나오는데 dialect 부분과 username, password, database, host를 자신의 환경에 맞게 수정합니다
**config/config.json**
```
{
  "development": {
    "username": "sa", //DB와 연결할 유저 "이름"
    "password": "********", //DB와 연결할 유저 "비밀번호"
    "database": "met_db", //사용할 Database 이름
    "host": "127.0.0.1", //DB 서버 호스트
    "dialect": "mssql" //DB 타입 설정 (mysql이 아니면 다른 DB 설정)
  },
  "test": {
    "username": "root",
    "password": null,
    "database": "database_test",
    "host": "127.0.0.1",
    "dialect": "mysql"
  },
  "production": {
    "username": "root",
    "password": null,
    "database": "database_production",
    "host": "127.0.0.1",
    "dialect": "mysql"
  }
}
```

 - migrations

 - models

**models/index.js**
```
'use strict';

const fs = require('fs');
const path = require('path');
const Sequelize = require('sequelize');
const basename = path.basename(__filename);
const env = process.env.NODE_ENV || 'development';
const config = require(__dirname + '/../../../config/config.json')[env];
const db = {};

let sequelize;

//config/config.js 파일에 있는 정보를 가져와 sequelize 객체를 생성한다.
if (config.use_env_variable) {
  sequelize = new Sequelize(process.env[config.use_env_variable], config);
} else {
  sequelize = new Sequelize(config.database, config.username, config.password, config);
}

// 우리가 작성한 Table파일을 찾아온다.
fs
  .readdirSync(__dirname)
  .filter(file => {
    return (file.indexOf('.') !== 0) && (file !== basename) && (file.slice(-3) === '.js');
  })
  .forEach(file => {
    const model = require(path.join(__dirname, file))(sequelize, Sequelize.DataTypes);
    db[model.name] = model;
  });

//DB에 모델이름을 연결한다.
Object.keys(db).forEach(modelName => {

  if (db[modelName].associate) {
    db[modelName].associate(db);
  }
});

db.sequelize = sequelize;
db.Sequelize = Sequelize;

module.exports = db;
```

 - seeders


## - DB 생성
sequelize에서 DB를 생성하는 방법은 터미널에 명령어를 통해 사용 가능합니다.
```
sequelize db:create
```
위 명령어를 통해 config/config.json 파일을 읽은 후, develpment 모드에 작성되어 있는 met_db DB가 생성이 됩니다.

- Model 생성

**models/Users.js**
```
module.exports = (sequelize, DataTypes) => {

    const Users = sequelize.define("Users", {
      id: {
        type: DataTypes.UUID,
        defaultValue: DataTypes.UUIDV4,
        primaryKey: true,
        comment: "고유번호 UUID",
      },
      email: {
        type: DataTypes.STRING(100),
        validate: {
          isEmail: true,
        },
        comment: "이메일",
      },
      password: {
        type: DataTypes.STRING(60),
        comment: "비밀번호",
      },
      name: {
        type: DataTypes.STRING(100),
        comment: "이름",
      },
      phone: {
        type: DataTypes.STRING(72),
        comment: "전화번호",
      },
    }, {
      charset: "utf8", // 한국어 설정
      collate: "utf8_general_ci", // 한국어 설정
      tableName: "Users", // 테이블 이름
      timestamps: true, // createAt & updateAt 활성화
      paranoid: true, // timestamps 가 활성화 되어야 사용 가능 > deleteAt 옵션 on
    });
  
    return Users;
  };
```

## - 연결

 - 프로젝트 루트에 실행 파일 작성

**server.js**
```
const { sequelize } = require('./models');

sequelize.sync({ force: false })
.then(() => {
    console.log('데이터베이스 연결 성공');
})
.catch((err) => {
    console.error(err);
});
```
server.js 파일, 즉, "서버를 실행하는 파일"에 해당 소스코드를 넣어 실행합니다.

```
node server.js
```

## - Ref
[[Node.js] Sequelize 개념 및 설치](https://any-ting.tistory.com/49)