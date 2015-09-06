# How Z-Level Improve Your Test Speed

Let say we have service layer like this:

```
app
├── controllers
├── libs
├── models
│   ├── Attachment.rb
│   ├── Comment.rb
│   ├── Contest.rb
│   ├── Prize.rb
│   └── User.rb
│
├── services
│   ├── user (this is a group service)
│   │   └── NotifyUser.rb (this is a service)
│   │
│   ├── z_comment_user
│   │   └── PostProfileComment.rb
│   │
│   ├── z_contest_user
│   │   └── JoinContest.rb
│   │
│   ├── zz_attachment_contest_user
│   │   └── SubmitProposal.rb
│   │
│   ├── zz_comment_contest_user
│   │   └── PostContestComment.rb
│   │
│   └── zz_contest_prize_user
│       └── ChooseWinner.rb
│
└── views
```
## Explanation

If service ```user/NotifyUser.rb``` changed, we need to run test for ```z_comment_user```,
```zz_attachment_contest_user```, ```zz_comment_contest_user```, ```zz_contest_prize_user```, 
**because these group of services might use one of service from ```user```** which is ```NotifyUser```.

But, it's gonna be a different case if service ```z_comment_user/PostProfileComment``` changed, 
we just only need to run test for ```zz_comment_contest_user```, Why??

Because group of service like ```zz_contest_prize_user``` definitely will never ever use one of 
```z_comment_user``` services, since ```z_comment_user``` services require class ```Comment``` to work
and services from ```z_contest_prize_user``` don't have it.

Check this picture to get better understanding:

<a href="url"><img src="https://cloud.githubusercontent.com/assets/1484308/9704803/e31af184-54dc-11e5-9794-2ff341861ffe.jpg" height="326" width="500" ></a>

A, B, C ,D is group services on Z-Level 0, then AB, AC, AD... is group services on Z-Level 1.

<a href="url"><img src="https://cloud.githubusercontent.com/assets/1484308/9704800/e30f93de-54dc-11e5-940f-79b475f1df4c.jpg" height="326" width="500" ></a>

This is what happens if we change one service in group of service A at Z-Level 0, we need to test all services that might contain it.

These are another example what to test if a service changed.

<a href="url"><img src="https://cloud.githubusercontent.com/assets/1484308/9704801/e31088f2-54dc-11e5-8ac9-e9eb80e95036.jpg" height="326" width="500" ></a>
<a href="url"><img src="https://cloud.githubusercontent.com/assets/1484308/9704804/ead18e9c-54dc-11e5-82c4-db821d903655.jpg"></a>
<a href="url"><img src="https://cloud.githubusercontent.com/assets/1484308/9705261/57b0622a-54e9-11e5-9f2c-781c05fb1107.jpg"></a>


## Test Speed Improvement

The old fashioned way to test is, TEST IT ALL!

Pros:
1. Make you feel more secure

Cons:
1. Wasting time
2. Break the developer concentration, because need to wait until the test done.

The real reason why people keep running all test is because 
* Their code was really tightly coupled
* Didn't have good structure to support the efficiency.

Nevertheless, if you're using this guide properly, you will also feel secure.
So, instead of keep wasting more time, [save it now!](README.md)
