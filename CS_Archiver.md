# C#のアーカイバ

.NET Framework 2.0 以降 では DotNetZip というライブラリで Zipファイルの圧縮、解凍が行える。
.NET Framework 4.5 以降 は標準で ZipArcive というクラスが使用できる。

-	DotNetZip (旧lonic Zip Library)
	-	https://github.com/haf/DotNetZip.Semverd
	-	MIT License
	-	パスワード付きファイル、ユニコード・ファイル名、ZIP64、AES暗号化、コメントを含むZIP形式
	-	Zip, Gzip, Zlib, Deflate, BZip2()

-	SevenZipSharp
	-	https://github.com/squid-box/SevenZipSharp
		-	[オリジナル](https://github.com/tomap/SevenZipSharp)のプロジェクトが終了したため、派生。
	-	 GNU GPL 3.0 license
	-	7z.dll を利用するための C#ラッパーライブラリ
	-	使用例
		-	https://blog.aspose.com/2020/05/06/create-7zip-archives-programmatically-using-csharp-asp.net/

-	SevenZipExtractor
	-	https://github.com/adoconnection/SevenZipExtractor
	-	MIT License
	-	7z.dll を利用するための C#ラッパーライブラリ

-	System.IO.Compression
	-	マイクロソフト純正のデフォルトのライブラリ
	-	Zip, Gzip, Zlib, Deflate, Brotli

-	LZMA SDK
	-	https://www.7-zip.org/sdk.html
	-	public domain.
	-	7-zip の公式ライブラリ


## 圧縮形式
-	Gzip(RFC 1952)
-	Brotli圧縮(RFC 7932) Http通信圧縮に利用される
-	Zlib
-	7-zip
	-	1999年7月～
	-	GNU LGPL
-	rar
	-	1995年～
	-	プロプライエタリ