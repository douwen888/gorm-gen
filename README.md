# GORM Gen

注意，本项目迁移自项目 gorm.in/gen@0.3.27

此处有做定制化修改：

1.生成的model中移除注释，防止生成的代码过长

使用本项目需要在项目中引入gorm和gorm-gen

```bash
go get -u gorm.io/gorm
go get -u github.com/zhangyiming748/gorm-gen
```
使用示例

```go
package main

import (
	"github.com/douwen888/gorm-gen"
	"github.com/huandu/xstrings"
	"gorm.io/driver/mysql"
	"gorm.io/gorm"
)

func main() {
	// 连接数据库
	dsn := "user:passwd@tcp(host:port)/dbname?charset=utf8mb4&parseTime=True&loc=Local"
	db, err := gorm.Open(mysql.Open(dsn), &gorm.Config{})
	if err != nil {
		panic(err)
	}

	// 初始化生成器
	g := gen.NewGenerator(gen.Config{
		//OutPath:      "internal/dao/gen", // dao 输出目录
		ModelPkgPath: "internal/model", // model 输出目录
	})

	// 使用数据库
	g.UseDB(db)
	// 映射所有整型为 int64
	g.WithDataTypeMap(map[string]func(columnType gorm.ColumnType) (dataType string){
		"tinyint":   func(gorm.ColumnType) string { return "int64" },
		"smallint":  func(gorm.ColumnType) string { return "int64" },
		"mediumint": func(gorm.ColumnType) string { return "int64" },
		"int":       func(gorm.ColumnType) string { return "int64" },
		"bigint":    func(gorm.ColumnType) string { return "int64" },
	})
	// 使用驼峰命名法
	g.WithJSONTagNameStrategy(func(columnName string) string {
		return xstrings.ToCamelCase(columnName)
	})
	// 生成全部表
	g.GenerateAllTable()
	// 执行生成
	g.Execute()

}
```
