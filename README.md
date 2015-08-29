# Single Responsibility Principle (SRP)
Major web frameworks today have an identical structure (MVC) and people usually use it in two ways.
```
app
├── controllers
├── libs
├── models
└── views
...
```
### 1. Fat Controller, Skinny Model

Bad:
- Not DRY because all business logic is inside the controller, you can't re-use it
- Hard to test

### 2. Skinny Controller, Fat Model

Bad:
- Creating God object

### 3. [SOLUTION] Skinny Controller, Skinny Model, Extracted Business Logic
Create a new layer for extracted business logic. Some people used to call this layer as Service Layer, but it's really up to you.

```
app
├── controllers
├── libs
├── models
├── services
└── views
...
```
Structure of implementation is the most common pitfall in service layer. So, I suggest you my structure.

#### 3.1 Namespace

- Every model class, should have at least one namespace in service layer.
- Every implementation class name inside service layer, should start with verb.

```
app
├── controllers
├── libs
├── models
│   └── User.rb
├── services
│   └── user
│       ├── Query.rb
│       ├── RegisterUser.rb
│       └── ResetPassword.rb
└── views
```
Service layer is following SRP, thus class RegisterUser should only have one responsbility, but **doesn't mean it must only have one method.**

```
class Services::User::RegisterUser
  def initialize(params)
    @_params = params
  end

  # the main process here.
  def process

  end

  private

    def checkUsernameAvailability

    end
end
```
Why method checkUsernameAvailability doesn't have it's own class? Doesn't it do some business logic there?

Yes, it is. But you must remember, the main purpose of SOLID Principle is **Maintainability**. Please don't over-do it.

Keep asking yourself, is it worth to pull it outside and create new class? What will I gain since this logic only used in registration process?

#### 3.2 Z-Level

Sometimes it's common for service to depends on 2 or more models.
- Create a namespace with those models name, sorted alphabetically
- Add Z in front of namespace, the more models it depends, the more Z added
```
app
├── controllers
├── libs
├── models
│   ├── Contest.rb
│   ├── Prize.rb
│   └── User.rb
├── services
│   ├── user
│   │   ├── Query.rb
│   │   ├── RegisterUser.rb
│   │   └── ResetPassword.rb
│   ├── z_contest_user
│   │   ├── Query.rb
│   │   └── JoinContest.rb
│   └── zz_contest_prize_user
│   │   ├── Query.rb
│       └── ChooseWinner.rb
└── views
```

Why Z?
- It sorts your services folder automatically, instantly give you the big picture
- Inevitably, Z-Level will have straight correlation with your simplicity, the higher Z-Level, the more coupled your SRP. So **it best to maintain your Z-Level low**.

#### 3.3 Libs
If there are business logic that doesn't fit in service layer, you should put it on Libs folder. e.g: Cache, Elasticsearch.

#### 3.4 Model and Controller
Let the model alone with relational definition and field validation. If you notice Query class inside user service layer namespace, it filled with all common query API that will be used by User class.

```
class Services::User::Query
  def initialize(user)
    @user = user
  end

  def getAllWinningContest

  end

  def getFollower

  end
end
```

Controllers just need to call service it needs to operate, that simple!

#### 3.5 Test
- If you change ```services/user/RegisterUser```, you only need to test ```services/user/RegisterUser```
- If you change ```models/User``` or ```services/user/Query```, you need to test ```services/user/*``` and all correlated Z-Level, ```[z]+[_user]+```
- If you change ```services/z_contest_user/Query```, you need to test ```services/z_contest_user/*``` and all correlated Z-Level, ```[z]+[_contest|_user]+```

Look at the pattern, the higher Z-Level changed, the smaller test coverage needed to run. It means your test more organized and faster. You can even create a simple bash script to generate which file is affected by your change.

#### 3.6 Summary
- Code more well-ordered
- Faster and easier to test
- DRY, you can also use business logic for API, even with an API versioning.

<a href="url"><img src="https://cloud.githubusercontent.com/assets/1484308/9563685/acf68676-4eb6-11e5-9f22-b9fd71310367.jpg" height="280" width="800" ></a>
