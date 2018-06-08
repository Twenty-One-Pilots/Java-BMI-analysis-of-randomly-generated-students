# Java-BMI-analysis-of-randomly-generated-students
BMI analysis of randomly generated students，by Java.

今天的Java实验的内容： 

题目：

> 1）编写OOBMI类文件；并在该类文件中定义另一个类Student，该类包含学号、姓名、身高、体重和bmi等属性，为Student类定义创建函数。 
2）在Student类中，增加public String toString()函数，该函数可以返回一个字符串，该字符串包含学号、姓名、身高、体重和计算后的bmi值，他们之间用制表符(\t)隔开； 
3）在OOBMI中增加成员属性Student[] students；在OOBMI中增加genStudents函数，参数为整数，能够随机生成指定数量的名学生对象，并保存到students数组中。（注意，学号、姓名、身高、体重等均需随机生成） 
4）在OOBMI类中增加public boolean isExists(String id) 函数，判断该学生是否已经在students数组中，函数返回值为boolean类型，如果已经存在，返回false；否则，返回true。 
5）修改OOBMI中的genStudents函数，调用isExists函数避免输入或生成重复的学号的学生。 
6）在OOBMI中增加4个函数，分别统计bmi的均值、中值、众数、方差等统计信息。 
7）在OOBMI中增加printStatics函数，该函数首先打印所有学生基本信息，然后打印bmi的均值、中值、众数、方差等统计结果信息。 
8）在OOBMI的main函数中，调用上述函数，完成输入、统计结果输出的完整过程。

由于初次接触面向对象编程，故耗费精力较多。

```
package oobmi;

import java.util.Random;
import java.math.BigDecimal;

public class OOBMI {

    final int num = 6;        //定义学生人数
    int i, j, k = 0;                //定义循环变量
    Student[] students = new Student[num];

    public static void main(String[] args) {
        OOBMI stu = new OOBMI();//实例化
        stu.genStudents(stu.num);
        stu.printStatics();
    }

    public void genStudents(int num) {
        students = new Student[num];
        for (i = 0; i < num; i++) {
            students[i] = new Student("", "", 1.7, 4, 2);       //实例化
            students[i].id = "HIT1172800" + genRandomId(3);  //随机生成学号
            students[i].name = genRandomName(3);              //随机生成姓名
            students[i].height = Math.random() / 2 + 1.5;         //随机生成身高（1.5~2）
            students[i].weight = Math.random() * 50 + 50;       //随机生成体重（50~100）
            BigDecimal h = new BigDecimal(students[i].height); //对身高保留两位小数
            students[i].height = h.setScale(2, BigDecimal.ROUND_HALF_UP).doubleValue();
            BigDecimal w = new BigDecimal(students[i].weight);  //对体重保留两位小数
            students[i].weight = w.setScale(2, BigDecimal.ROUND_HALF_UP).doubleValue();
            if (!(isExists(students[i].id))) {
                i--;                                     //判断学号是否重复
            }
        }
    }

    public boolean isExists(String id) {
        for (int j = 0; j < i - 1; j++) {//注意使用j以与genStudents()中的循环变量i区分
            if (id.compareTo(students[j].id) == 0) //判断学号是否与前面的重复
            {
                return false;
            }
        }
        return true;
    }

    public double ave(Student students[]) {
        double ave = 0;
        for (i = 0; i < num; i++) {         //计算bmi总和
            ave += students[i].bmi;
        }
        ave /= num;                             //计算平均数
        return ave;
    }

    public double med(Student students[]) {
        int i, j;
        double temp;
        Student TempArray[] = new Student[num];
        System.arraycopy(students, 0, TempArray, 0, num);
        for (i = 0; i < num; i++) {                       //将bmi排序
            for (j = i + 1; j < num; j++) {
                if (TempArray[j].bmi < TempArray[i].bmi) {
                    temp = TempArray[j].bmi;
                    TempArray[j].bmi = TempArray[i].bmi;
                    TempArray[i].bmi = temp;
                }
            }
        }
        if (num % 2 == 0) {                         //就奇偶个学生分类
            return (TempArray[num / 2].bmi + TempArray[num / 2 - 1].bmi) / 2;
        } else {
            return TempArray[(num - 1) / 2].bmi;
        }
    }

    public double[] mod(Student students[]) {
        int times[] = new int[num];     //定义每一个数值在其后出现的次数构成的数组
        double mode[] = new double[num];//定义众数和0构成的数组
        int max = 0;                            //定义最大的次数
        for (i = 0; i < num; i++) {
            for (j = i; j < num; j++) {
                if (students[i].bmi == students[j].bmi) {
                    times[i]++;                 //对数组times[]赋值
                }
            }
        }
        max = times[0];
        for (i = 0; i < num; i++) {            //使max的值为最大次数
            if (max <= times[i]) {
                max = times[i];
            }
        }
        for (i = 0; i < num; i++) {
            if (max == times[i]) {
                mode[i] = students[i].bmi;      //对数组mode[]赋值
            }
        }
        return mode;                        //返回mod[]
    }

    public double var(Student students[]) {
        double sum = 0;
        for (i = 0; i < num; i++) {
            sum += students[i].bmi;
        }
        double ave = sum / num;             //计算平均值
        double var = 0;
        for (i = 0; i < num; i++) {
            var += (students[i].bmi - ave) * (students[i].bmi - ave);//方差的计算
        }
        var /= num;
        return var;                        //返回方差
    }

    public void printStatics() {
        System.out.println("id\t\t姓名\t身高\t体重\tbmi");          //输出表头
        for (i = 0; i < num; i++) {
            System.out.println(students[i].toString());         //输出学生信息
        }

        System.out.println("bmi的平均数为 " + String.format("%.2f", ave(students)) + ";");
        System.out.println("bmi的中位数为 " + String.format("%.2f", med(students)) + ";");
        String str = "";                    //创建众数构成的字符串
        for (k = 0; k < num; k++) {     //注意使用j以与mod()中的循环变量i,j区分
            if (mod(students)[k] != 0) {
                str = str + " " + mod(students)[k];
            }
        }
        System.out.println("bmi的众数为" + str + ";");
        System.out.println("bmi的方差为 " + String.format("%.2f", var(students)) + ".");
    }

    public static String genRandomName(int length) {
        String str = "ABCDEFGHIJKLMNOPQRSTUVWXYZ";//随机生成的姓名的每一位的选择
        Random random = new Random();                 //实例化random
        StringBuffer sb = new StringBuffer();         //实例化sb
        for (int j = 0; j < length; j++) {
            int number = random.nextInt(26);
            sb.append(str.charAt(number));
        }
        return sb.toString();
    }

    public static String genRandomId(int length) {
        String str = "1234567890";                   //随机生成的id后三位每一位的选择
        Random random = new Random();               //实例化random
        StringBuffer sb = new StringBuffer();           //实例化sb
        for (int j = 0; j < length; j++) {
            int number = random.nextInt(10);
            sb.append(str.charAt(number));
        }
        return sb.toString();
    }
}

class Student {

    String id;              //定义学生id
    String name;        //定义学生姓名
    double height;      //定义学生身高
    double weight;      //定义学生体重
    double bmi;           //定义bmi

    public String toString() {
        bmi = weight / height / height;     //计算bmi
        BigDecimal bm = new BigDecimal(bmi);
        bmi = bm.setScale(2, BigDecimal.ROUND_HALF_UP).doubleValue();//对bmi四舍五入保留两位小数
        return id + "\t" + name + "\t" + height + "\t" + weight + "\t" + bmi;
    }

    public Student(String id, String name, double height, double weight, double bmi) {//定义创建函数
        this.id = id;                                           //赋值
        this.name = name;
        this.weight = weight;
        this.height = height;
        this.bmi = bmi;
    }
}
```
