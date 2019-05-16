# SchoolGuardSystem
人证合一项目实训——校园出入管理登记系统 
方法声明
allowedPeople(String message)

用途：
判断用户是否能够进入

返回：
//家长来访
{
    "result":"allowed or notallowed",
    "details":{
        "id":"id",//身份证号
        "identity":"value",//身份
        "imgnow":"当前照片"
	"imglog":"注册照片"
	"imgstu":"学生照片"
	"name":"value"
	"stuName":"value"
	"teacherName":"value"
	"class":"value"
	"teacherPhone":"value"
    }
}

//黑名单
{
    "result":"notallowed",
    "details":{
        "id":"id",//身份证号
        "identity":"value",//身份
        "imgnow":"当前照片"
	"imglog":"注册照片"
	"contactPosition":"value"//负责人职务
	"contactName":"value"//负责人
	"contactPhone":"value"//负责人联系方式
	"note":"value"//备注

    }
}


//学生出入
{
    "result":"allowed or notallowed",
    "details":{
        "id":"id",//学号
        "identity":"value",//身份
        "imgnow":"当前照片"
	"imglog":"注册照片"
	"name":"value"
	"class":"value"
	"teacherName":"value"
	"teacherPhone":"value"
    }
}
********************************************************
数据库工具包
++++++++++++++++++++++++++++++++++++++++++++++++
根据身份证号查询外来人员信息（接收身份证号）

方法声明：
getMessage(String id, String name)

select * from parent left join student on parent.sid=student.sid left join worker on student.tid=worker.wid where parent.id=?
select * from schoolsys.blacklist left join schoolsys.worker on blacklist.contactid=worker.wid where id=?
接收身份证号和身份证上的姓名
	{
	    "id": "value",	
	    "name": "valeue"
	}
返回：
	亲属：
	{
	    "identity": "parent",
	    "message": {
	        "student": "name.",
	        "teacher": "name",
	        "id": "id",
	        "class": "value",
	        "name": "name",
	        "teacherphone": "value",
	        "studentgender": "value"
	    }
	}
	黑名单：
	{
	    "identity": "blacklist",
	    "message": {
	        "id": "id",
	        "note": "value",
	        "name": "name",
	        "personincharge": "value",//负责人名字
	        "personinchargephone": "value",//负责人联系方式
	        "personinchargeposition": "value"//负责人职务
	    }
	}
	无结果
	{
	    "identity": "noresult",
	    "message": {
	        "id": "id",
	        "name": "name"
	    }
	}

+++++++++++++++++++++++++++++++++++++++++++++++++++++++
返回所有学生和教职工图片（无接收数据）
方法声明：
getAllpic()


select sid,spic from student
select wid,wpic from worker
{
    "picture": [
        {
            "id": "id",
            "pic": "base64"
        },
        {
            "id": "id",
            "pic": "base64"
        },
        {
            "id": "id",
            "pic": "base64"
        }
    ],
    "total": "value"
}
++++++++++++++++++++++++++++++++++++++++++++++++++++++
增加来访信息

方法声明：
addNewRecord(String message) 

insert into schoolsys.record (name,identity,status,pic,sid,tid,time) values(?,?,?,?,?,?,?)

	接收：
	{
	    "name": "value",
	    "identity": "value",//具体身份
	    "status": "value",
	    "sid": "value",//若为学生、教职工，则为空
	    "tid": "value",//若为学生、教职工，则为空
	    "date": "value",
	    "pic": "value",
	}
	返回：
	{
	    "message": "ok/fail"
	}
++++++++++++++++++++++++++++++++++++++++++++++++++
修改在校状态

方法声明：
setScPeopleStay（String message）

update schoolsys.worker set isschool='N' where wid='0';
update schoolsys.parent set isschool='N' where id='0';
update schoolsys.student set isschool='N' where sid='0';
	接收：
	{
	    "identity": "value",//身份
	    "id": "value",
	    "status": "value"//入校还是出校
	}
	返回：
	{
	    "message": "ok/fail"
	}
++++++++++++++++++++++++++++++++++++++++++++++++
修改学生请假状态

方法声明：
setStudentCanleave（String message）

SQL:
update schoolsys.student set canleave='Y' where sid='0';

	接收
	{
	    "id": "value",
	    "status": "value"//是否请假
	}
	返回
	{
	    "message": "ok/fail"
	}

++++++++++++++++++++++++++++++++++++++++++++++++
查询所有来访记录

方法声明：
String getAllRecord()

SQL:
select a.id,a.name,a.identity,a.status,c.sname,d.wname,a.time from schoolsys.record a,schoolsys.student c,schoolsys.worker d where a.sid=c.sid and a.tid=d.wid ;

return：
{
    "total": "value",
    "message": [
        {
            "id": "value",
            "name": "value",
            "identity": "value",
            "status": "value",
            "sname": "value",
            "tname": "value",
            "time": "value"
        }
    ]
}
+++++++++++++++++++++++++++++++++++++++++++++++++++
查看某条具体记录（接收String id）

方法声明：
String getRecordDetailById(String id)

sql:
select a.id,a.name,a.identity,a.status,c.sname,d.wname,a.time,a.pic as newpic,b.pic as logpic from schoolsys.record a,schoolsys.parent b,schoolsys.student c,schoolsys.worker d where a.id=b.id and a.sid=c.sid and a.tid=d.wid and a.id=0;


return:
{
    "id": "value",
    "name": "value",
    "identity": "value",//具体身份
    "status": "value",
    "sname": "value",//若为学生、教职工，则为空
    "tname": "value",//若为学生、教职工，则为空
    "date": "value",
    "newpic": "value",
    "logpic": "value"
}
++++++++++++++++++++++++++++++++++++++++++++++++++++++
