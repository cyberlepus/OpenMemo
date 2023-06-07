C# Chart
================================================================================

手順
----------------------------------------------------------------

- 「NuGetパッケージ管理」から、Microsoft.Chart.Controls をインストール
- ツールボックス > データ > Chart  を Form上に配置
- プロパティ > Series > ChartType で 外観を設定する。


プログラムでの操作
----------------------------------------------------------------
- `using System.Windows.Forms.DataVisualization.Charting`
- Chart : チャートを表示するControlクラス
- Series : グラフ表示部分
- Legend : 凡例データ項目の一覧表示



Chart.Legends.Points に x,y の数値を設定できる。
1次元の棒グラフや円グラフ等は y値のみの設定でよい。

Point.Label はチャート内で表示されるテキスト。
Point.LabelToolTip はチャート内のLabel上にカーソルが来た時に、表示されるツールチップ表示
Point.ToolTip はチャート内の表示範囲上にカーソルが来た時に、表示されるツールチップ表示
Point.LegentText は 凡例内で表示されるテキスト。
Point.LegentToolTip は 凡例内で表示されるテキストにカーソルが来た時に、表示されるツールチップ表示



Chart.Legends.Points[n].
            var HitTestResult = chart1.HitTest(e.X, e.Y);
            // クライアント座標をグラフ上の座標に変換する
            var elemType = HitTestResult.ChartElementType;
https://docs.microsoft.com/ja-jp/dotnet/api/system.windows.forms.datavisualization.charting.chartelementtype?
          PlottingArea
          DataPoint
          DataPointLabel
          LegendItem

共通
----------------------------------------------------------------
*** データ項目を指定する ***
- series の追加
```
    var series = new Series();
	  series.ChartType = SeriesChartType.Bar;
    series.ChartArea = area.Name;						// 表示する領域を名前で指定
				:
    chart.Series.Add(series);			// 最後にチャートに追加
```

*** データ項目を指定する ***


*** 表示する範囲を指定する ***

- グラフと軸を合わせたチャート範囲の位置
  - ChartArea.Position は Chart全体エリアでの範囲を100分率で指定する。
  - ChartArea.InnerPlotPosition は ChartArea.Position で指定したエリア範囲をを100分率で指定する。
	  - 軸は指定によって、グラフの内側だったり外側だったりするので、InnerPlotPosition を使って必要に応じてスペースを確保しておく
- Legend の位置
	- Legend.Position は Chart全体エリアでの範囲を100分率で指定する。
	- 例
  ```C#
		var series = chart.Series[0];
		var area = chart.ChartAreas[series.ChartArea];
  	area.Position.FromRectangleF(new RectangleF(0, 34, 100, 64));
  	area.InnerPlotPosition.FromRectangleF(new RectangleF(40, 10, 55, 90));
	```



*** マウスカーソル位置を取得する ***

	```
  var HitTestResult = chart1.HitTest(e.X, e.Y);
            // クライアント座標をグラフ上の座標に変換する
            var elemType = HitTestResult.ChartElementType;
	```
	https://docs.microsoft.com/ja-jp/dotnet/api/system.windows.forms.datavisualization.charting.chartelementtype?
    PlottingArea
    DataPoint
    DataPointLabel
    LegendItem

*** マウスカーソル位置から対象を取得する ***



横棒グラフ
----------------------------------------------------------------
- 複数のバー表示
	- 同じChartArea に Series を複数設定する。
	- 

- その他
	- area.AxisX.LabelStyle.Interval
	- area.AxisY.LabelStyle.Interval
		軸の表示をスキップさせる。例えば 2 なら、軸が2つ毎に表示される。

	- area.AxisX.ScaleView.Position 
	- area.AxisX.ScaleView.Position
		軸の起点位置から表示位置をずらす。スクロール可能な場合に使うが、
    項目が1つで本来スクロールしない場合でも強制的に位置をずらすので、項目数以下の数で指定する。



円グラフ
----------------------------------------------------------------

using System.Drawing;
  Color, RectangleF を使うため。

ChartArea の Position と InnerPlotPosition は 固定値を設定しておく。
そうしないと、XY軸の項目の文字数によってプロットエリアが変化する。
スクロール設定だとスクロールで表示項目が変化するため、それに合わせてプロットエリアも変化することもある。


*** TabにCloseボタンとAddボタンを付ける ***
https://stackoverflow.com/questions/36895089/tabcontrol-with-close-and-add-button



C# Form
================================================================================
ListBox

- 選択値を取得
	- Indexえ取得
	GetEnumerator() の 返り値が IEnumerator なので型が不明。キャストしてやる必要がある。
	- (IEnumerable<int>)listBox.SelectedIndices.GetEnumerator()	

