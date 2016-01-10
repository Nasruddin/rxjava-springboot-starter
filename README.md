# rxjava-springboot-starter
A demo implementation of RxJava

Documentation is in process !

    Event              Iterable(Pull)               Observable(Push)

    Retrieve data      T next()                     onNext(T)
    discover error     throws Exception             onError(Exception)
    Complete           returns                      onCompleted()


                       Single                       Multiple

    Sync               T getData()                  Iterable<T> getData()
    Async              Future<T> getData            Observable<T> getData()

    Observable.from(doSomeHeavyTask()) --------------------> Observable
    .subscribeOn(Schedulers.io)        --------------------> Schedulers
    .subscribe(getObserver)            --------------------> Observers


    *Lazy
    *Cold(Some kind of filesystem doing nothing) or
    *Hot(Something getting fired all the time like stock info)
    *Asynchronous
    *Push
    public class Observable<T> {
        public static <T> Observable<T> create(OnSubscribe<T> f);
        public Subscription subscribe(Observer<? super subscriber>);
    }

    onNext | onError | onCompleted
    public interface Observer<T> {
        public abstract void onCompleted();
        public abstract void onError(Throwable e);
        public abstract void onNext(T t);
    }

    public static interface OnSubscribe<T> {
        public void call (Subscriber<? super T> subscriber);
    }

    public abstract class Subscriber<T> implements Observer<T>, Subscription {
        public abstract void onCompleted();
        public abstract void onError(Throwable e);
        public abstract void onNext(T t);

        public final void add(Subscription s) {
            public void setProducer(Producer producer);
        }

        public interface Producer {
            public void request(long n);
        }
    }

    //Hello World Example
    //Synchronous single value that runs on Main Thread(Single Thread)
    Observable.create(subscriber -> {
        subscriber.onNext("Hello World !");
        subscriber.onCompleted();
    }).forEach(System.out::Println);

    //Synchronous multiple value
     Observable.create(subscriber -> {
            subscriber.onNext("Hello");
            subscriber.onNext("World");
            subscriber.onNext("!");
            subscriber.onCompleted();
        }).forEach(System.out::Println);

    //Asynchronous Single value
    Observable.create(subscriber -> {
        try{
            subscriber.onNext(doSomething());
            subscriber.onCompleted();
        } catch(Throwable e) {
            subscriber.onError(e); //Error Notification
        }
        }).subscribeOn(Schedulers.io()) //Asynchronous Single value call so forEach statement will run on some other Thread
            .forEach(System.out::Println);

    //Unsubscribe Example
    //Its holds a infinite loop so this is keep pushing data but take(10) tells it
    //stop when you get 10. And unSubscribed halt process
    Observable.create(subscriber -> {
        int i = 0;
        while(!subscriber.isUnsubscribed()){
            subscriber.onNext(i++);
        }
        }).take(10).forEach(System.out::println);
