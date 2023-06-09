解答方向：String类的不可变常量+StringBuffer的可变以及线程安全+StringBuilder的可变以及线程不安全

1.String类被设置为final，保证了其不能被继承，其内部用来存储字符的数组被声明为了final，表示其不能引用其他数组，并且String内部没有操作这个数组的方法，所以保证了String创建的对象的不可变性。这种不可变性的作用是：1.String类经常作为Hashmap的key，当计算hash值时由于string不变，所以hash值可以被缓存，不需要重复计算。2.配合字符串常量池的使用，只有当创建出来的字符串对象不可变时，才能被其他变量放心的作为常量引用。3.参数的不可变性，string经常来作为参数，如果其值在程序运行时被改变，那么会导致参数含义的变化，使程序不安全。4.线程安全，不可变的对象必然是线程安全的。

2.StringBuffer是可变的，其继承了抽象的字符串构造器这个类，其内部维护了一个字符数组可被改变，其通过append（），replace（），insert（），reverse（）等方法改变这个字符序列，通过toString（）和subString（）方法来转换成一个不可变的string对象，改变这个对象的方法通过加锁来保证了线程安全

3.StringBuilder是可变的，其和StringBuffer相同，但是没有加锁，不保证线程安全，适合单线程下使用，效率更高。