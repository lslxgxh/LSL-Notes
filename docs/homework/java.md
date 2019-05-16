test1_1

```java
package test1_1;

class Student {

    //学号
    private String studentNumber;

    //姓名
    private String studentName;

    //专业
    private String specially;

    //学费
    private double tuition;

    public String getStudentNumber() {
        return studentNumber;
    }

    public void setStudentNumber(String studentNumber) {
        this.studentNumber = studentNumber;
    }

    public String getStudentName() {
        return studentName;
    }

    public void setStudentName(String studentName) {
        this.studentName = studentName;
    }

    public String getSpecially() {
        return specially;
    }

    public void setSpecially(String specially) {
        this.specially = specially;
    }

    public double getTuition() {
        return tuition;
    }

    public void setTuition(double tuition) {
        if (tuition >= 4500) {
            this.tuition = tuition;
        } else {
            System.out.println("最低学费为4500元,你设置的学费为" + tuition + "元");
        }

    }
}

```

```java
package test1_1;

class StudentTest {
    public static void main(String[] args) {

        Student Tom = new Student();

        Tom.setStudentNumber("0121603490401");
        Tom.setStudentName("Tom");
        Tom.setSpecially("信管");
        Tom.setTuition(4300.19);
        Tom.setTuition(4800.34);
        System.out.println(Tom.getSpecially() + "专业的" + "学号为" + Tom.getStudentNumber() + "的" + Tom.getStudentName() +"同学"
                + "需交学费" + Tom.getTuition() + "元。");
    }
}


```

test1_2

```java
package test1_2;

class Book {

    //图书名称
    private String bookName;

    private int[] ratings = new int[5];

    public String getBookName() {
        return bookName;
    }

    public void setBookName(String bookName) {
        this.bookName = bookName;
    }

    protected void addRating(int numRated) {
        if (numRated>5||numRated<1) {
            System.out.println("等级设置错误，有效值为1-5");
            return;
        }
        ratings[numRated-1]++;
    }

    protected double getAverage() {
        double sum = 0;
        for(int i = 1; i <= 5; i++) {
            sum += ratings[i-1]*i;
        }
        return sum/5;
    }

   /* //将该图书评级为5星（很好）的人数
    private int numRated5 = 0;

    //将该图书评级为4星（好）的人数
    private int numRated4 = 0;

    //将该图书评级为3星（一般）的人数
    private int numRated3 = 0;

    //将该图书评级为2星（差）的人数
    private int numRated2 = 0;

    //将该图书评级为1星（很差）的人数
    private int numRated1 = 0;

    protected void addRating(int numRated) {
        switch (numRated){
            case 1:
                numRated1 ++;
                break;
            case 2:
                numRated2 ++;
                break;
            case 3:
                numRated3 ++;
                break;
            case 4:
                numRated4 ++;
                break;
            case 5:
                numRated5 ++;
                break;
                default: System.out.println("The numRated is wrong!");
        }
    }

    protected double getAverage() {
        return ((double)(numRated1 + numRated2*2 + numRated3*3 + numRated4*4 + numRated5*5) / 5);
    }*/
}

```

```java
package test1_2;

import jdk.internal.cmm.SystemResourcePressureImpl;

class BookReview {
    public static void main(String[] args) {
        Book book = new Book();
        book.setBookName("Head First 设计模式");

        for (int i = 0; i < 5; i++) {
            int num = (int)(Math.random() * 5 + 1);
            book.addRating(num);
        }

        System.out.println(book.getBookName() + "的评分为" + book.getAverage());
    }
}


```

```java
package test1_3;

import java.text.DecimalFormat;

class Account {

    //账号
    private int accountNumber;

    //用户名
    private String accountName;

    //余额
    private double balance;

    public  Account(){}

    public Account(int accountNumber, String accountName, double balance) {
        this.accountName = accountName;
        this.accountNumber = accountNumber;
        this.balance = balance;
    }

    DecimalFormat df = new DecimalFormat( "0.00 ");

    public int getAccountNumber() {
        return accountNumber;
    }

    public void setBalance(double balance) {
        this.balance += balance;
    }

    @Override
    public String toString() {
        return "账号:"+accountNumber+"  用户名：" + accountName + "   余额：" + df.format(balance);
    }
}
```

```java
package test1_3;

import java.util.ArrayList;
import java.util.Scanner;

class AccountTest {
    public static void main(String[] args) {
        //操作用户
        Account operatingAccount = new Account();

        Scanner sc = new Scanner(System.in);

        ArrayList<Account> accountArrayList = new ArrayList<Account>();
        Account account1 = new Account(100001, "王一", Math.random()*10000);
        Account account2 = new Account(100002, "赵二", Math.random()*10000);
        Account account3 = new Account(100003, "张三", Math.random()*10000);
        Account account4 = new Account(100004, "李四", Math.random()*10000);
        Account account5 = new Account(100005, "王五", Math.random()*10000);

        accountArrayList.add(account1);
        accountArrayList.add(account2);
        accountArrayList.add(account3);
        accountArrayList.add(account4);
        accountArrayList.add(account5);

        System.out.println("请输入您的账号");

        while (sc.hasNextInt()){
            boolean flag = false;
            int input = sc.nextInt();
            for(Account account : accountArrayList) {
                if(account.getAccountNumber() == input) {
                    operatingAccount = account;
                    System.out.println("请输入存款金额");
                    flag = true;
                }
            }
            if(flag) {
                break;
            }
            System.out.println("账号输入错误！请重新输入：");
        }

        while (sc.hasNextDouble()){
            double input = sc.nextDouble();
            if (input > 0) {
                int flag = -1;
                operatingAccount.setBalance(input);
                System.out.println("是否继续存款？yes or no");
                Scanner scanner = new Scanner(System.in);
                while (scanner.hasNextLine()){
                    String in =scanner.nextLine();
                    if (in.equals("yes")){
                        System.out.println("请输入存款金额");
                        flag = 1;
                        break;
                    } else if(in.equals("no")){
                        flag = 0;
                        break;
                    } else {
                        System.out.println("输入错误请重新输入");
                        System.out.println("是否继续存款？yes or no");
                        continue;
                    }
                }
                if (flag == 1) {
                    continue;
                } else if (flag == 0) {
                    break;
                }
            } else {
                System.out.println("存款金额不得小于0元");
                System.out.println("请重新输入存款金额");
            }
        }

        System.out.println(operatingAccount.toString());

    }
}
```

```java
package test2_1;

import java.text.DecimalFormat;

import sun.plugin2.main.client.PrintBandDescriptor;

class BankAccount {

    private String accountNumber;

    private double balance;

    public String getAccountNumber() {
        return accountNumber;
    }

    public double getBalance() {
        return balance;
    }

    DecimalFormat df = new DecimalFormat( "0.00 ");

    BankAccount(String accountNumber, double balance) {
        this.accountNumber = accountNumber;
        this.balance = balance;
    }

}
```

```java
package test2_1;

class SavingsAccount extends BankAccount {


    private double interetRate;

    public double getInteretRate() {
        return interetRate;
    }

    SavingsAccount(String accountNumber, double balance, double interetRate) {
        super(accountNumber, balance);
        this.interetRate =interetRate;
    }
}
```

```java
package test2_1;

class Test {
    public static void main(String[] args) {
        SavingsAccount savingsAccount = new SavingsAccount("95588", 4500, 0.30);
        System.out.println("账号" + savingsAccount.getAccountNumber() +
                "余额" + savingsAccount.getBalance() +
                "利率" + savingsAccount.getInteretRate());
    }
}
```

```java
package test2_2;

class Employee {

    protected String employeeID;

    protected String name;

    protected String position;

    protected double salary;

    Employee (String employeeID, String name, String position) {
        this.employeeID = employeeID;
        this.name = name;
        this.position = position;
    }

    public String getName() {
        return name;
    }

    public void setSalary(double para) {
    }

    public double getSalary() {
        return salary;
    }
}

class Manager extends Employee {

    Manager(String employeeID, String name, String position) {
        super(employeeID, name, position);
    }
    @Override
    public  void setSalary(double para) {
        this.salary = 5000 + para * 0.01;
    }
}

class Salesperson extends Employee {
    Salesperson(String employeeID, String name, String position){
        super(employeeID, name, position);
    }

    @Override
    public  void setSalary(double para) {
        this.salary = 3000 + para * 0.05;
    }
}

class TemporaryWorker extends Employee {
    TemporaryWorker(String employeeID, String name, String position){
        super(employeeID, name, position);
    }
    public  void setSalary(int para) {
        this.salary = para * 80;
    }
}
```

```java
package test2_2;

import sun.awt.geom.AreaOp;

class EmployeeTest {
    public static void main(String[] args) {
        Employee m = new Manager("123456","张三","manager");
        Employee s1 = new Salesperson("123457","李四","sale");
        Employee s2 = new Salesperson("123458","王五","sale");
        Employee t = new TemporaryWorker("123459", "王一", "temp");

        m.setSalary(12345.5);
        s1.setSalary(1451);
        s2.setSalary(1254.41);
        ((TemporaryWorker) t).setSalary(8);

        System.out.println(m.getName()+"的工资为"+m.getSalary());
        System.out.println(s1.getName()+"的工资为"+s1.getSalary());
        System.out.println(s2.getName()+"的工资为"+s2.getSalary());
        System.out.println(t.getName()+"的工资为"+t.getSalary());
    }
}
```
