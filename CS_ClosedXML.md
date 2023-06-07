ClosedXML
================================================================================


バイトストリームから読み込み、書き込み
----------------------------------------------------------------
- workbook の 読み込み時には、ファイル名指定と、ストリーム指定の2つの方法がある。
- ファイル名指定は内部でファイルストリームを取得するので、基本的にはストリームから読み出す仕組みになっている。
- 読み込み後も workbook 内部でストリームを保持しており、書き込み時のストリームを使いまわすため、ストリームを Close() してはならない。
  - Close()してしまうと、書き込み時、Save(), SaveAs()の両方で ObjectDisposeException 例外となる。
- Save()は内部で保持しているストリームを使用して書き込むので、拡張可能なストリームである必要がある。
- SaveAs()は引数でストリームを指定しそちらに書き出します。内部的に保持していたストリームは、指定したストリームと置き変わります。
　- この時、読み込み時に使用していたストリームは Close() されない。
  - つまり、SaveAs()では読み込み時に使用したストリームを Close() する事が出来ない。


ストリームを使用しての読み込み
```
  var memStream = new MemoryStream();
  var workbook = XLWorkbook(memStream);
```
ファイル名を指定しての読み込み
```
  var workbook = XLWorkbook(filePath);
```









範囲にアクセス
----------------------------------------------------------------
```
var worksheet = workbook.Worksheet("SheetName");
var worksheet = workbook.Worksheet(n);    // 左からn番目(1～)のシートを選択

var worksheet = workbook.AddWorksheet("SheetName",1);   // 指定した位置に挿入される。

```

範囲にアクセス
----------------------------------------------------------------
IXLAddress                  特定のセルの位置を指す
IXLAddress.RowNumber        行位置。1から開始
IXLAddress.ColumNumber      列位置。1から開始
IXLAddress.ColumLetter      A1表記のカラム文字列になる。
IXLAddress.WorlkSheet       ワークシート

IXLRangeAddress             セル ～ セル２点間の範囲を指す
IXLRangeAddress.RowSpan     範囲内の行数
IXLRangeAddress.ColumSpan   範囲内の列数
IXLRangeAddress.WorlkSheet  ワークシート

IXLRange


IXLRange range = worksheet.Range(row, clm);


セルの値にアクセス
----------------------------------------------------------------

cell.CachedValue    object型  入っているデータ本体。
cell.Value          式の結果。式でない場合は空白。式が失敗(Excel上では#N/A)する場合は例外となる
cell.FormulaA1      式の内容。
cell.CopyTo()       セルをコピーする。式の中にある相対位置指定も、移動先に合わせて変更される。<br>Excelのコピーと同等だがExcelにある「形式を選択してコピー」ができない。

HasFomula == true
  cell.CachedValue  式の結果  string形式
  cell.Value        例外      (例外にならない場合もある)
  cell.FormulaA1    式(A1形式)
  cell.FormulaR1C1  式(R1C1形式)
HasFomula == false
  cell.CachedValue  object型 で DataType によって異なる。
  cell.Value        object型 で DataType によって異なる。
  cell.FormulaA1    string.Empty
  cell.FormulaR1C1  string.Empty

  cell.CachedValue, Value
    object型 で以下の様に扱われる

    数値  の場合は double型     DataType == XLDataType.Number         1000                  右揃えになる
    日付  の場合は DataTime型   DataType == XLDataType.DataTime       2021/05               日付表示になる。
    リンクの場合は string型     DataType == XLDataType.Text           http://google.com     ハイパーリンクになる。
    文字列の場合は string型     DataType == XLDataType.Text           ああああ
    正誤値の場合は bool型       DataType == XLDataType.Boolean        true , false          Excel上。大文字の TRUE, FALSEになる。

  FormulaA1, FormulaR1C1は、それぞれの形式で表した文字列となる。
  N3のCellに"=SUM(A3:A5)"とした場合、
    FormulaA1   = "SUM(A3:A5)"
    FormulaR1C1 = "SUM(RC[-13]:R[2]C[-13])"
  N3のCellに"=SUM($A$3:$A$5)"とした場合、
    FormulaA1   = "SUM($A$3:$A$5)"

    FormulaR1C1 = "SUM(R3C1:R5C1)"

シリアル値の日付
  Excel は日付を入力した場合、1900/1/1を1とした総日数で値を管理する。この値をシリアル値という。
  表示の際には書式設定にて日付に変換する。値だけをコピーするとシリアル値の数値がコピーされる。
  シリアル値については: https://www.waenavi.com/entry/20190830/1567156477

  シリアル値には閏年の判定にバグがあって、1900/2/29のみ本来ない閏年が計算されている。
  https://qiita.com/wagase/items/6444ce93a24aaaa74981
  つまり、
  1900/1/01～2/28:1～59
  1900/2/29      :60(欠番にすべき)
  1900/3/01～以降:61～連番
  が正しい。

  CachedValue は string型, DataType == XLDataType.Text で保存されている。
  Value, GetString()は例外

  値からは数値を文字列化したものとは区別がつかないので、IXLCell.Style.NumberFormat.Format  を取得して
  日付文字があるかどうかを判定するしかない。


文字列で取得
  GetString() はいろいろな条件で例外が発生する
  Value も同様
  表示形式が指定されている場合、それを変換して文字列にする方法が存在しない。
  (COM)を使うしかない？
  IXLCell.RichText.Text で、値をおおよそ取得できる。

----------------------------------------------------------------
表示形式
IXLCell.Style.NumberFormat.Format で取得ができる。

+   ((ClosedXML.Excel.XLStyle)((ClosedXML.Excel.XLStylizedBase)cell).InnerStyle).DateFormat {-1-yyyy/m/d;@} ClosedXML.Excel.IXLNumberFormat {ClosedXML.Excel.XLNumberFormat}


その他
----------------------------------------------------------------
シートの表示スケール指定
sheet.SheetView.ZoomScale
sheet.SetTabActive();


range.Clear();    Rowに指定されている Height と Column に指定されているWidth が消えない
rows.Clear()    幾つかの Hieght 設定は消えているが、
          アウトラインのグループ指定を行っている部分は消えない

https://elleneast.com/?p=12201


DataValidation
  入力規則

ライブラリについて
================================================================================

COM
----------------------------------------------------------------
- エクセル本体で使用しているコンポーネント
  - 表示されないがエクセルにてファイルを開いて操作をしているのと同じ動作を行う。
  - 処理的に重いが、エクセルと同等の処理が行えるのが強み


OpenXML
----------------------------------------------------------------
- マイクロソフトの純正ライブラリ
- その名の通り、Excel, Word, PowerPoint等で使われている新しいフォーマット形式のベースとなっている OpenXML を扱うためのライブラリ。
- 汎用データ形式を扱うライブラリなので、Excelのデータを扱うには色々と面倒なことが多い。

https://www.projectgroup.info/tips/Microsoft.NET/tips_0005.html

ClosedXML
----------------------------------------------------------------
- ソースリポジトリ:"https://github.com/ClosedXML/ClosedXML"
- ドキュメント:"https://closedxml.readthedocs.io/en/latest/"
- 参考
  - https://www.projectgroup.info/tips/Microsoft.NET/tips_00
  - https://closedxml.readthedocs.io/en/latest/05.html

- マイクロソフト推奨ライブラリ

- 問題点
- ファイルの内容によっては、保存するとファイルが破壊されるが、復元は可能。何の条件で起きるかは未調査。
- 他のライブラリと比較して処理が重い。シートのコピーが非常に時間(数分から)がかかる。
- 範囲のコピーが数式のみ。

NPOI(Apatch POI)
----------------------------------------------------------------
- Java ライブラリからの C# 移植。
- 基本的にCellやRangeではなく、Row -> Column で扱う。<br>
  ただし、ヘルパークラス、関数がある為そちらを使うことで、同じような操作が可能。
- インデクサや get/set アクセサをあまり使わないので、C#ライクに書けない。
- xls, xlsx 両方に対応。
- 問題点
  - 条件付き書式が正しく保存されない。設定としては残っているが、書式設定が何故か色が反映されない。
  - ファイルの内容によっては、保存するとファイルが破壊されて復元ができない事がある。何の条件で起きるかは未調査。

https://www.e-iceblue.com/Tutorials/Spire.XLS/NPOI.html
----------------------------------------------------------------
