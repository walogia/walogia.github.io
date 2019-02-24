# PMD文件结构

[参考自](https://blog.goo.ne.jp/torisu_tetosuki/e/209ad341d3ece2b1b4df24abf619d6e4)

## Head

・ヘッダ
char magic[3]; // "Pmd"
float version; // 00 00 80 3F == 1.00 // 訂正しました。コメントありがとうございます
char model_name[20];
char comment[256];

"Pmd" 1.00 "Test Model" "Comments"

・文字列: 終端 0x00、 パディング 0xFD
　文字コード：シフトJIS
※終端の0x00は省略不可(例外あり)

## 