## - 개요
이번 시간에는 조금 더 세부적으로 모델을 만들어 보는 시간을 가져보겠습니다.

 

모델을  정의(생성)하는 방법은 sequelize.difine 함수를 통해서 만들 수 있습니다.

 

지난 시간에 소스 코드를 가져와서 설명해 보겠습니다.

 
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
sequelize.define 함수에 3가지 파라미터 값이 들어갑니다.


>> 1. 모델(테이블) 이름 설정
>> 2. 컬럼 정의
>> 3. 모델 옵션 정의

### 1. 모델(테이블) 이름 설정
시퀄 라이즈는 테이블 이름을 추론합니다. 무슨 말이냐면, 만약에 첫 번째 파라미터로 "User"라고 정의를 해도 "users"라고 생성이 됩니다.
그 이유는 테이블 이름 옵션(tableName : "User") 또는 복수화 중지(freezeTableName : true) 옵션을 설정하지 않았다면 시퀄 라이즈는
테이블 이름을 users라고 지정되어 생성합니다. (저는 항상 tableName 옵션을 설정합니다. :>)

### 2. 컬럼 정의
두 번째 파라미터는 우리가 만들려고 하는 모델(테이블) 컬럼 속성을 정의합니다.
대표적으로 자주 사용하는 속성들에 대해 설명하겠습니다.

 - type : 데이터 타입을 정의(문자, 숫자, 날짜 등등)
 - primaryKey : 기본 키 설정(default : false) 시퀄 라이즈에서는 기본적으로 - primaryKey 컬럼을 생성한다.(id라는 이름으로 생성)
 - autoIncrement : 숫자 자동 증가(default : false)
 - allowNull : NOT NULL 허용 여부(default : true)
 - unique : Unigue 조건인지 아닌지에 대한 옵션
 - comment : column에 대한 설명 작성 가능
 - validate : 데이터 유효성 검사를 하는 속성

공식 홈페이지 나와 있는 속성은 아래와 같습니다. 
```
    validate: {
      is: /^[a-z]+$/i,          // matches this RegExp
      is: ["^[a-z]+$",'i'],     // same as above, but constructing the RegExp from a string
      not: /^[a-z]+$/i,         // does not match this RegExp
      not: ["^[a-z]+$",'i'],    // same as above, but constructing the RegExp from a string
      isEmail: true,            // checks for email format (foo@bar.com)
      isUrl: true,              // checks for url format (http://foo.com)
      isIP: true,               // checks for IPv4 (129.89.23.1) or IPv6 format
      isIPv4: true,             // checks for IPv4 (129.89.23.1)
      isIPv6: true,             // checks for IPv6 format
      isAlpha: true,            // will only allow letters
      isAlphanumeric: true,     // will only allow alphanumeric characters, so "_abc" will fail
      isNumeric: true,          // will only allow numbers
      isInt: true,              // checks for valid integers
      isFloat: true,            // checks for valid floating point numbers
      isDecimal: true,          // checks for any numbers
      isLowercase: true,        // checks for lowercase
      isUppercase: true,        // checks for uppercase
      notNull: true,            // won't allow null
      isNull: true,             // only allows null
      notEmpty: true,           // don't allow empty strings
      equals: 'specific value', // only allow a specific value
      contains: 'foo',          // force specific substrings
      notIn: [['foo', 'bar']],  // check the value is not one of these
      isIn: [['foo', 'bar']],   // check the value is one of these
      notContains: 'bar',       // don't allow specific substrings
      len: [2,10],              // only allow values with length between 2 and 10
      isUUID: 4,                // only allow uuids
      isDate: true,             // only allow date strings
      isAfter: "2011-11-05",    // only allow date strings after a specific date
      isBefore: "2011-11-05",   // only allow date strings before a specific date
      max: 23,                  // only allow values <= 23
      min: 23,                  // only allow values >= 23
      isCreditCard: true,       // check for valid credit card numbers

      // Examples of custom validators:
      isEven(value) {
        if (parseInt(value) % 2 !== 0) {
          throw new Error('Only even values are allowed!');
        }
      }
      isGreaterThanOtherField(value) {
        if (parseInt(value) <= parseInt(this.otherField)) {
          throw new Error('Bar must be greater than otherField.');
        }
      }
    }
```

### 3. 모델 옵션 정의
세 번째 파라미터는 모델 옵션을 설정합니다. 대표적으로 사용하는 옵션은 아래와 같습니다.

 - charset : "utf8" // charset & collate를 둘 다 설정해야 한글이 입력된다.
 - collate : "utf8_general_ci" // 여기서 설정을 안 했다면 DB를 생성할 때 설정해 주면 된다
 - tableName : 테이블 이름 설정
 - freezeTableName : true로 설정하면 이름을 복수로 설정하지 않는다.
 - timestamps : 기본적인 설정은 true입니다. createdAt과 updatedAt 컬럼이 생성되며, 데이터가 생성되는 시간과 수정되는 시간을 나타내는 옵션입니다.

createdAt 또는 updatedAt 중 하나만 활성하고 싶다면 아래와 같이 정의할 수 있습니다.
```
{
    timestamps: true,
    createdAt: true,
    updatedAt: false,
}
```
위와 같이 선언하면 createdAt은 활성화되고 updatedAt은 비활성화가 됩니다. 속성을 반대로 설정하면 반대로 활성화가 된다. :)

 - paranoid

paranoid 옵션은 deletedAt이라는 컬럼이 추가됩니다. 데이터를 삭제하라는 쿼리가 들어왔을 때 해당 데이터를 삭제하지 않고
삭제되는 시점을 등록한다.

백업 서버나 데이터를 복구하기 쉽게 하기 위해서 자주 사용됩니다.

**설정 조건** : timestamps가 true로 설정되어야 합니다.

 - underscored : 시퀄 라이즈는 기본적으로 테이블명과 컬럼명을 캐멀 케이스로 만듭니다. 이 옵션을 true로 설정하면 이름을 스네이크 케이스 형식으로 바꿔주는 옵션입니다.

 - defaulteValue  : 기본 값을 설정합니다.
이번 시간에는 모델을 정의하고 생성하는 법에 대해 알아봤습니다. 다음 시간에는 모델 간에 관계에 대해 알아보겠습니다.