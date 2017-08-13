```
unit Unit1;

interface

uses

Windows, Messages, SysUtils, Classes, Graphics, Controls, Forms, Dialogs,

inifiles, //配置操作文件

StdCtrls, ExtCtrls;

type

TForm1 = class(TForm)

Edit1: TEdit;

CheckBox1: TCheckBox;

Edit2: TEdit;

Label1: TLabel;

Label2: TLabel;

Timer1: TTimer;

Label3: TLabel;
    ComboBox1: TComboBox;
    Button1: TButton;

procedure FormCreate(Sender: TObject);

procedure FormDestroy(Sender: TObject);

procedure Timer1Timer(Sender: TObject);
    procedure Button1Click(Sender: TObject);

private

{ Private declarations }

public

{ Public declarations }

end;

var

Form1: TForm1;

implementation

var

myinifile:TInifile;

{$R *.DFM}

procedure TForm1.FormCreate(Sender: TObject);

var

filename:string;

begin

filename:=ExtractFilePath(paramstr(0))+'myini.ini';

myinifile:=TInifile.Create(filename);

edit1.Text:= myinifile.readstring('程序参数','用户名称','缺省的用户名称');

edit2.text:= inttostr(myinifile.readinteger('程序参数','已运行时间',0));

checkbox1.Checked:= myinifile.readbool('程序参数','是否正式用户',False);

end;

procedure TForm1.FormDestroy(Sender: TObject);

begin

myinifile.writestring('程序参数','用户名称',edit1.Text);

myinifile.writeinteger('程序参数','已运行时间',strtoint(edit2.text));

myinifile.writebool('程序参数','是否正式用户',checkbox1.Checked);

//myinifile.Destroy;
myinifile.Free;

end;

procedure TForm1.Timer1Timer(Sender: TObject);

begin

edit2.Text:=inttostr(strtoint(edit2.text)+1);
//myinifile.writestring('程序参数','用户名称'+edit2.text,edit1.Text);

end;

procedure TForm1.Button1Click(Sender: TObject);
var
strList:TStringList ;
i:integer;

begin

    strList := TStringList.Create;
    myinifile.ReadSection('程序参数', strList)    ;
  for i:=0 to strList.Count -1 do
  begin  
    //添加项，关联值  
    ComboBox1.AddItem(strList.ValueFromIndex[i], TObject(strList.Names[i]));
  end;  
  strList.Free;

end;

end.
```