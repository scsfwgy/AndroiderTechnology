## 设计模式

> 设计模式不是一两个简单的demo可以讲清楚的，关键：**灵活运用**，禁忌**过度设计**

创建型模式，共五种：工厂方法模式、抽象工厂模式、单例模式、建造者模式、原型模式。
结构型模式，共七种：适配器模式、装饰器模式、代理模式、外观模式、桥接模式、组合模式、享元模式。
行为型模式，共十一种：策略模式、模板方法模式、观察者模式、迭代子模式、责任链模式、命令模式、备忘录模式、状态模式、访问者模式、中介者模式、解释器模式。


## 行为型模式
#### 模板模式
* 干什么用？
	*  模板方法模式是类的行为模式。准备一个抽象类，将部分逻辑以具体方法以及具体构造函数的形式实现，然后声明一些抽象方法来迫使子类实现剩余的逻辑。不同的子类可以以不同的方式实现这些抽象方法，从而对剩余的逻辑有不同的实现。这就是模板方法模式的用意。简而言之：通用的业务逻辑让父类（基类）去实现，具体业务逻辑（不同的子类不同的业务）强制子类去自己实现。

* 构成
	* 抽象类（AbstractClass）：实现了模板方法，定义了算法的骨架。
	* 具体类（ConcreteClass)：实现抽象类中的抽象方法，已完成完整的算法。
* 分类

* demo :一个人去学校的例子


		// 父类：完成公共的操作；并且定义子类必须要完善的操作，具体实现延迟到子类去完成。
		public abstract class AbstractPerson {
			// 抽象类定义整个流程骨架
			public void prepareGotoSchool() {
				sleep();
				dressUp();
				eatBreakfast();
				takeThings();
				goHome();
			}
		
			// 以下是父类要完成的公共操作
			private void sleep() {
				System.out.println("磨牙、做梦、放屁");
			}
		
			private void goHome() {
				System.out.println("晚上回家。。。");
			}
		
			// 以下是不同子类根据自身特性完成的具体步骤
			protected abstract void dressUp();
		
			protected abstract void eatBreakfast();
		
			protected abstract void takeThings();
		}


		// 学生子类
		public class StudentPerson extends AbstractPerson {
		
			@Override
			protected void dressUp() {
				System.out.println("学生穿校服");
		
			}
		
			@Override
			protected void eatBreakfast() {
				System.out.println("学生吃早餐");
		
			}
		
			@Override
			protected void takeThings() {
				System.out.println("学生去学校听课。");
		
			}
		}

	//老师子类
	public class TeacherPerson extends AbstractPerson {

		@Override
		protected void dressUp() {
			System.out.println("老师穿正装");
	
		}
	
		@Override
		protected void eatBreakfast() {
			System.out.println("老师吃早餐");
	
		}
	
		@Override
		protected void takeThings() {
			System.out.println("老师去学校讲听课。");
	
		}
	}

	// 该模式非常有用，特别是项目前期架构的时候可以考虑把一些公共的操作放到基类中去处理，对于具体业务延迟到子类中去实现。
	public class Test {
		public static void main(String[] args) {
			AbstractPerson abstractPerson = new StudentPerson();
			abstractPerson.prepareGotoSchool();
			System.out.println("--------");
			abstractPerson = new TeacherPerson();
			abstractPerson.prepareGotoSchool();
			System.out.println("-----");
			System.out
					.println("通过上面的demo我们可以看出有通用的业务逻辑是在父类实现的，具体不同的业务逻辑是由各个子类实现的。");
	
		}
	}



## 结构型模式
#### 适配器模式
* 干什么用？
	* 将一个类的接口转换成客户希望的另外一个接口。Adapter 模式使得原本由于接口不兼容而不能一起工作的那些类可以一起工作。

* 构成
	* 被适配类：一个普通类。
	* 一个客户端需要的接口：定义需要的接口。
	* 适配器类：在该类中实现接口，在接口方法中去**调用被适配的类**的方法。

* 分类
	* 类适配器
	* 方法适配器
	
* demo：

