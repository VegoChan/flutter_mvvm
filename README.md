# mvvm_builder

mvvm_builder is a Flutter plugin to help you implement MVVM design pattern with flutter. 
MVVM = Model - View - ViewModel

## Installation

To use this plugin, add mvvm_builder as a [dependency in your pubspec.yaml file](https://flutter.io/platform-plugins/).  

## Why use MVVM design patterns

Implement a good design pattern can just simplify code, make more code testable, allow better performance...
The idea is to split business logic from your view. Your view has to stay dumb, and this plugin will help you 
to respect the pattern. 


## Usage

1 - import MVVMPage Widget from package
```
import 'package:mvvm_builder/mvvm_builder.dart';
```

### 2 - Create your Model
```
class MyViewModel extends MVVMModel {
  String title;
  List<TodoModel> todoList;
}

class TodoModel {
  String title, subtitle;

  TodoModel(this.title, this.subtitle);
}
```


### 3 - Create your Presenter

```
class MyPresenter extends Presenter<MyViewModel> {

  MyPresenter(MyViewModel model, MyViewInterface view) : super(model, view);

  @override
  Future onInit() async {
    this.viewModel.title = "My todo list";
    this.viewModel.todoList = List();
    // init your view model here -- load from network etc ... 
    for(int i = 0; i < 15; i++) {
      this.viewModel.todoList.add(new TodoModel("TODO $i", "my task $i"));
    }
    this.refreshView(); // call this at the end if onInit takes time
  }
}
```
### 4 - define your view interface
for example: 
```
abstract class MyViewInterface {
  
  void showMessage(String message);
  
}
```


### 5 - Create your Page
You page must implement your view interface. For example :
```
class _MyAppState extends State<MyApp> implements MyViewInterface{
  
  final GlobalKey<ScaffoldState> _scaffoldKey = GlobalKey();

  @override
  void initState() {
    super.initState();
  }


  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: MVVMPage<MyPresenter, MyViewModel>(
        builder: (context, presenter, model) {
          return Scaffold(
            key: _scaffoldKey,
            appBar: AppBar(title: Text(model.title)),
            body: ListView.separated(
              itemBuilder: (context, index) => InkWell(
                onTap: () => presenter.onClickItem(index),
                child: ListTile(
                  title: Text(model.todoList[index].title),
                  subtitle: Text(model.todoList[index].subtitle),
                ),
              ),
              separatorBuilder: (context, index) => Divider(height: 1) ,
              itemCount: model.todoList.length
            )
          );
        },
        presenter: MyPresenter(new MyViewModel(), this),
      )
    );
  }

  @override
  void showMessage(String message) {
    _scaffoldKey.currentState.showSnackBar(new SnackBar(content: Text(message)));
  }
}
```

## Test
This pattern is really usefull for testing as it allows you to test business logic separately from rendering. 
The another good thing of this pattern is the ability to test your view with many combination of viewModel settings 
without the need of chaining actions before having the desired view state. 
You can now test your view alone, presenter alone and test them together. 

Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.
