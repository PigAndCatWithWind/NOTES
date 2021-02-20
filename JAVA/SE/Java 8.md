# Java 8

## 方法引用

*方法引用：若lambda体中的内容有方法已经实现，我们可以使用“方法引用”*

*(可以理解为方法引用是Lambda表达式的另一种表现形式)*

主要有三种语法格式：

1. 对象::实例方法名
2. 类::静态方法名
3. 类::实例方法名

注意：

1. Lambda体中调用方法的参数列表与返回值类型，要与函数式接口中抽象方法的函数列表和返回值类型保持一致
2. 若Lambda参数列表中第一参数是实例方法的调用者，而第二个参数是实例方法的参数时，可以使用ClassName::Method

## 构造器引用

格式：

ClassName::new

注意：需要调用的构造器的参数列表要与函数式接口中抽象方法的参数列表保持一致

## 数组引用

格式：

Type[]::new



## Stream

#### 注意：

1. Stream 自己不会存储元素
2. Stream不会改变源对象，相反，他们会返回一个持有结果的新Stream
3. Stream操作是延迟执行。这意味着他们会等到需要结果的时候才执行

#### Stream的操作三个步骤：

1. 创建Stream -> 一个数据源（如：集合、数组）获取一个流
2. 中间操作 -> 一个中间操作链，对数据源的数据进行处理
3. 终止操作（终端操作）-> 一个终止操作，执行中间操作链，并产生结果

### Stream 创建

1. 通过Collection 系列集合提供的sream()或paralleStream（）

   ```java
   List<String> list = new ArrayList<>();
           Stream<String> stringStream = list.stream();
   ```

2. 通过Arrays中的静态方法stream()获取数组流

```java
 Employee[] employee = new Employee[10];
        Stream<Employee> employeeStream = Arrays.stream(employee);
```

3. 通过Arrays中的静态方法of()

```java
Stream<String> stream = Stream.of("a","bb","ccc");
```

4. 创建无限流

```java
//迭代
Stream<Integer> integerStream = Stream.iterate(0,x -> x+2);

//生成
Stream<Double> doubleStream = Stream.generate(() -> Math.random());
```

### 中间操作

*中间操作：不会执行任何操作*

#### 筛选与切片

1. filter ---> 接受Lambda，从流中排除某些元素

   ```java
   List<Employee> employees = Arrays.asList(
               new Employee("zhangfei",18,9999.99),
               new Employee("lisi",58,5555.55),
               new Employee("wuangwu",26,3333.33),
               new Employee("liuliu",36,6666.66),
               new Employee("qianqi",12,8888.88)
       );
    //内部迭代：迭代操作由Stream API 完成
       @Test
       public void test2(){
           employees.stream()
                   .filter((e) -> e.getAge() > 35)
                   .forEach(System.out::println);
       }
   //外部迭代
       @Test
       public void test3(){
           Stream<Employee> employeeStream =
                   employees.stream().filter(e -> e.getAge() >35);
           Iterator<Employee> iterator = employeeStream.iterator();
           while (iterator.hasNext())
               System.out.println(iterator.next());
       }
   
   ```

   

2. limit ---> 截断流，使其元素不超过给定数量

   ```java
       @Test
       public void test4(){
           employees.stream()
                   .filter((e) -> e.getSalary() > 5000)
                   .limit(2)
                   .forEach(System.out::println);
       }
   ```

   

3. skip(n) ---> 跳过元素，返回一个仍掉了前n个元素的流。若流中元素不足n个，则返回一个空流。与limit(n)互补

```java
    @Test
    public void test5(){
        employees.stream()
                .filter((e) -> e.getSalary()>5000)
                .skip(2)
                .forEach(System.out::println);
    }
```



1. distinct ---> 筛选，通过流所生成元素的hashCode()和equals() 去除重复元素

```java
package Lambda;

import java.util.Objects;

/**
 * @创建人 PigAndCatWithWind
 * @创建时间 2021/2/2上午11:22
 * @描述
 */
public class Employee {
    private String name;
    private int age;
    private double salary;

    public Employee(){

    }

    public Employee(String name,int age,double salary){
        super();
        this.name = name;
        this.age = age;
        this.salary = salary;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public double getSalary() {
        return salary;
    }

    public void setSalary(double salary) {
        this.salary = salary;
    }

    @Override
    public String toString() {
        return "Employee{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", salary=" + salary +
                '}';
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof Employee)) return false;
        Employee employee = (Employee) o;
        return age == employee.age &&
                Double.compare(employee.salary, salary) == 0 &&
                Objects.equals(name, employee.name);
    }

    @Override
    public int hashCode() {
        return Objects.hash(name, age, salary);
    }
}


List<Employee> employees = Arrays.asList(
            new Employee("zhangfei",18,9999.99),
            new Employee("zhangfei",18,9999.99),
            new Employee("lisi",58,5555.55),
            new Employee("lisi",58,5555.55),
            new Employee("wuangwu",26,3333.33),
            new Employee("liuliu",36,6666.66),
            new Employee("liuliu",36,6666.66),
            new Employee("qianqi",12,8888.88)
    );


@Test
    public void test6(){
        employees.stream()
                .distinct()
                .forEach(System.out::println);
    }

```



#### 映射

1. map ---> 接受Lambda，将元素转换成其他形式或提取信息，接收一个函数作为参数，该函数会被应用到每个元素上，并将其映射成一个新的元素

   ```java
   @Test
       public void test7(){
           stringList.stream()
                   .map(TestStream::filterCharacter)
                   .forEach(s -> s.forEach(System.out::println));
       }
   ```
   

   
2. flatMap ---> 接收一个函数作为参数，将流中的每个值都换成另一个流，然后把所有流连接成一个流

```java
@Test
    public void test8(){
        stringList.stream()
                .flatMap(TestStream::filterCharacter)
                .forEach(System.out::println);
    }
```

```java
public static Stream<Character> filterCharacter(String s){
        List<Character> list = new ArrayList<>();
        for (Character c: s.toCharArray()) {
            list.add(c);
        }
        return list.stream();
    }
```

#### 排序

1. sorted() ---> 自然排序（Comparable）递增

   ```java
   @Test
       public void test9(){
           stringList.stream()
                   .sorted()
                   .forEach(System.out::println);
       }
   ```

   

2. sorted(Comparator com) ---> 定制排序

```java
@Test
    public void test10(){
        employees.stream()
                .sorted((e1,e2) -> {
                    if (e1.getAge()==e2.getAge()){
                        return e1.getName().compareTo(e2.getName());
                    }else {
                        return Integer.compare(e1.getAge(),e2.getAge());
                    }
                }).forEach(System.out::println);
    }
```



## 终止操作

*终止操作：一次性执行全部内容，即“惰性求值”*

1. allMatch ---> 检查是否匹配所有元素

   ```java
   @Test
       public void test11(){
           boolean b = employees.stream().allMatch(e -> e.getStatus().equals(Employee.Status.BUSY));
           System.out.println(b);
       }
   ```

   

2. anyMatch ---> 检查是否至少匹配一个元素

   ```java
   @Test
       public void test12(){
           System.out.println(
                   employees.stream()
                   .anyMatch(
                           employee -> employee.getStatus().equals(Employee.Status.VOCATION)
                   )
           );
       }
   ```

   

3. noneMatch ---> 检查是否没有匹配所有元素

   ```java
   @Test
       public void test13(){
           System.out.println(
                   employees.stream()
                           .noneMatch(
                                   employee -> employee.getStatus().equals(Employee.Status.BUSY)
                           )
           );
       }
   ```

   

4. findFirst ---> 返回第一个元素

   ```java
    @Test
       public void test14(){
           System.out.println(
                   employees.stream()
                   .sorted((e1,e2) ->{
                       if (e1.getSalary()==e2.getSalary()){
                           return Integer.compare(e1.getAge(),e2.getAge());
                       }else {
                           return -Double.compare(e1.getSalary(),e2.getSalary());
                       }
                   }).findFirst()
                           .get()
                           .toString()
           );
       }
   ```

   

5. findAny ---> 返回当前流这中任意元素

   ```java
   @Test
       public void test15(){
           Optional<Employee> optionalEmployee= employees.stream()
                   .filter(employee -> employee.getStatus().equals(Employee.Status.FREE))
                   .findAny();
           System.out.println(optionalEmployee.get());
       }
   ```

   

6. count ---> 返回流中元素的总个数

   ```java
    @Test
       public void test16(){
           System.out.println(
                   employees.stream()
                   .count()
           );
       }
   ```

   

7. max ---> 返回其中最大值

   ```java
   @Test
       public void test17(){
           System.out.println(
                   employees.stream()
                   .max((e1,e2) -> Double.compare(e1.getSalary(),e2.getSalary()))
                   .get()
           );
       }
   ```

   

8. min ---> 返回其中最小值

   ```java
   @Test
       public void test18(){
           System.out.println(
                   employees.stream()
                   .map(Employee::getSalary)
                   .min(Double::compare)
                   .get()
           );
       }
   ```

   

#### 归约

reduce(T identity , BinaryOperator) / reduce(BinaryOperator) ---> 可以将流中元素反复结合起来，得到一个值。

```java
 @Test
    public void test19(){
        List<Integer> list = Arrays.asList(1,2,3,4,5,6,7,8,9);
        int sum = list.stream()
                .reduce(0,(x,y) -> x+y);
        System.out.println(sum);
    }
    @Test
    public void test20(){
        Optional<Double> optionalDouble =
                employees.stream()
                .map(Employee::getSalary)
                .reduce(Double::sum);
        System.out.println(optionalDouble.get());
    }
```



#### 收集

collect ---> 将流转化为其他形式，接收一个Collector接口的实现类，用于给Stream中元素汇总的方法

```java
@Test
    public void test21(){
        List<String> stringList =
                employees.stream()
                .map(Employee::getName)
                .collect(Collectors.toList());
        System.out.println(stringList);
    }

    @Test
    public void test22(){
        HashSet<String> stringHashSet =
                employees.stream()
                .map(Employee::getName)
                .collect(Collectors.toCollection(HashSet::new));
        stringHashSet.forEach(System.out::println);
    }

    @Test
    public void test23(){
        Double d = employees.stream()
                .collect(Collectors.averagingDouble(Employee::getSalary));
        System.out.println(d);
    }

    @Test
    public void  test24(){
        Optional<Employee> optionalEmployee =
                employees.stream()
                .collect(Collectors.maxBy((e1,e2)->Double.compare(e1.getSalary(),e2.getSalary())));
        System.out.println(optionalEmployee.get());
    }

    @Test
    public void test25(){
        Optional<Double> optionalDouble =
                employees.stream()
                .map(Employee::getSalary)
                .collect(Collectors.minBy(Double::compare));
        System.out.println(optionalDouble.get());
    }

    @Test
    public void test26(){
        Map<Employee.Status,List<Employee> > statusListMap =
                employees.stream()
                .collect(Collectors.groupingBy(Employee::getStatus));
        System.out.println(statusListMap);
    }

    @Test
    public void test27(){
        Map<Employee.Status,Map<String, List<Employee> > > mapMap =
                employees.stream()
                .collect(Collectors.groupingBy(Employee::getStatus,Collectors.groupingBy((Employee e) -> {
                    if (e.getAge()<=35){
                        return "yong";
                    }else if (e.getAge()<=50){
                        return "zhong";
                    }else{
                        return "old";
                    }
                })));
        System.out.println(mapMap);
    }

    @Test
    public void test28(){
        Map<Boolean,List<Employee>> listMap =
                employees.stream()
                .collect(Collectors.partitioningBy(e -> e.getSalary()>8000));
        System.out.println(listMap);
    }

    @Test
    public void test29(){
      DoubleSummaryStatistics doubleSummaryStatistics =
              employees.stream()
                      .collect(Collectors
                              .summarizingDouble(Employee::getSalary));
      System.out.println(doubleSummaryStatistics.getAverage());
      System.out.println(doubleSummaryStatistics.getMax());
      System.out.println(doubleSummaryStatistics.getCount());
      System.out.println(doubleSummaryStatistics.getMin());
      System.out.println(doubleSummaryStatistics.getSum());
    }

    @Test
    public void test30(){
        String s = employees.stream()
                .map(Employee::getName)
                .collect(Collectors.joining("--->"));
        System.out.println(s);
    }
```



## Optional类



1. Optional.of(T t) ：创建一个Optional实例
2. Optional.empty()：创建一个空的Optional实例
3. Optional.ofNullable(T t)：若t不为null，创建Optional实例，负责创建空实例
4. isPresent()：判断是否包含值
5. orElse(T t)：如果调用对象含值，返回该值，否则返回t
6. orElseGet(Supplier s) ： 如果对象包含值，并返回处理后的Optional，否则返回s 获取值
7. map(Function f)：如果有值对其处理，返回处理后的Optional，否则返回Optional.empty()
8. flatMap(Function mapper)：与map类相似。要求返回值必须是Optional