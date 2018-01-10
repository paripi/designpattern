# デザインパターン

## Strategyパターン

- メソッド内でif文などで分岐させてアルゴリズムを実装している場合、切り替えや追加を容易に行うために、別クラスとして作成する。

状況に応じてアルゴリズムを変える...  
例えば、難易度の設定によってアルゴリズムを変える。
1. 簡単な、大小の比較を行うアルゴリズムを考える。  
人間を表すHumanクラス：名前、身長、体重、年齢の4つのパラメータを持つ
```java
public class Human{

    public String name;
    public int height = -1;
    public int weight = -1;
    public int age = -1;

    public Human(String name,int height,int weight,int age){
        this.name = name;
        this.height = height;
        this.weight = weight;
        this.age = age;
    }
}
```
  - 2つのHumanインスタンスが与えられた場合に、それらの大小を比較するSampleClassというクラスを考える。
```java
public class SampleClass{
    public int compare(Human h1,Human h2){
        if(h1.age > h2.age){
            return 1;
        }else if(h1.age == h2.age){
            return 0;
        }else{
            return -1;
        }
    }
}
```
    - 第一引数で渡されたHumanインスタンスの年齢のほうが大きければれば1
    - 2つのHumanインスタンスの年齢が同じであれば0
    - 第2引数で渡されたHumanインスタンスの年齢のほうが大きければ-1
  - ここでは年齢だけに注目しているが、他にもパラメータは存在し、比較方法もたくさんある。
1. そこで、比較するパラメータを指定できるようなプログラムとすることを考える。
```java
public class SampleClass{
  private int type = -1;
  public static final int COMPARE_AGE = 1;
  public static final int COMPARE_HEIGHT = 2;
  public static final int COMPARE_WEIGHT = 3;

  public SampleClass(int compareType){
  	this.type = type;
  }

  public int compare(Human h1,Human h2){
	  if(type == COMPARE_AGE){
	    if(h1.age > h2.age){
  	    return 1;
    	}else if(h1.age == h2.age){
    		return 0;
    	}else{
    		return -1;
    	}
	  }else if(type == COMPARE_HEIGHT){
      if(h1.height > h2.height){
  	    return 1;
    	}else if(h1.height == h2.height){
    		return 0;
    	}else{
    		return -1;
    	}
	  }else if(type == COMPARE_WEIGHT){
      if(h1.weight > h2.weight){
  	    return 1;
    	}else if(h1.weight == h2.weight){
    		return 0;
    	}else{
    		return -1;
    	}
	  }
  }
}
```
  - このようにif文の分岐を考えると煩雑でメンテナンス性に乏しいソースコードとなる。
1. ここで、比較アルゴリズム部分をクラスとして分離する。
  - たとえば、まずは、年齢を比較するためのAgeComparatorクラスを作成。  
  (ただし、まだStrategyパターンにもなっていないし、メリットもない。)
```java
public class AgeComparator{
  public int compare(Human h1, Human h2){
    if(h1.age > h2.age){
      return 1;
    }else if(h1.age == h2.age){
      return 0;
    }else if(h1.age < h2.age){
      return -1;
    }
  }
}
```

  - 実際の比較処理は、
```java
public class MyClass{
  public int compare(Human h1, Human h2){
    return new AgeComparator().compare(h1,h2);
  }
}
```
   - AgeComparatorクラス以外にも、身長を比較するためのHeightComparatorクラス、体重を比較するためのWeightComparatorクラス、なども必要。  
1. これらの分離したクラスに共通のインターフェース、Comparatorインターフェースを持たせる。

```java
public interface Comparator{
  public int compare(Human h1, Human h2);
}

public class AgeComparator implements Comparator{
  public int compare(Human h1, Human h2){
    if(h1.age > h2.age){
      return 1;
    }else if(h1.age == h2.age){
      return 0;
    }else if(h1.age < h2.age){
      return -1;
    }
  }
}

public class HeightComparator implements Comparator{
//略
}

public class WeightComparator implements Comparator{
//略
}
```

とすることで、SampleClassは以下のようになる。

```java
public class MyClass{
 private Comparator comparator = null;
 public MyClass(Comparator comparator){
  this.comparator = comparator;
 }
 public int compare(Human h1, Human h2){
  return new AgeComparator().compare(h1,h2);
 }
}
```

このようにアルゴリズムの部分を別クラスとして作成することで、比較アルゴリズムの追加が簡単になる。


[TECHSCORE 第10章.Strategyパターン](http://www.techscore.com/tech/DesignPattern/Strategy.html/)より