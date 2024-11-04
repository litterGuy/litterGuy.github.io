---
layout: post
title:  "graphql+beego的使用"
date:   2024-11-04 13:52:41
categories: 
   - golang
tags:
   - golang
---

# GraphQL 使用

1. 安装和初始化 gqlgen
```
go install github.com/99designs/gqlgen@latest
gqlgen init
```
初始化后，会自动生成文件目录
```
.                                                                                                                                                                                                                                   
├── gqlgen.yml
├── graph
│   ├── generated.go
│   ├── model
│   │   └── models_gen.go
│   ├── resolver.go
│   ├── schema.graphqls
│   └── schema.resolvers.go
└── server.go
```
删除 graph 文件夹下的所有生成文件

2. 定义 GraphQL Schema
在graph文件夹下创建schema.graphqls 文件
```
# schema.graphqls

type User {
  id: ID!
  name: String!
  email: String!
}

type Query {
  user(id: ID!): User
  users: [User!]!
}
```
3.  运行 gqlgen 自动生成代码
```
gqlgen generate
```
4. 实现 Resolvers
在生成的 schema.resolvers.go 文件内实现逻辑
```
package graph

import (
    "context"
    "myapp/graph/model"
)

// QueryResolver 实现了用户的查询逻辑
func (r *queryResolver) User(ctx context.Context, id string) (*model.User, error) {
    return &model.User{
        ID:    id,
        Name:  "John Doe",
        Email: "john@example.com",
    }, nil
}

func (r *queryResolver) Users(ctx context.Context) ([]*model.User, error) {
    return []*model.User{
        {ID: "1", Name: "John Doe", Email: "john@example.com"},
        {ID: "2", Name: "Jane Doe", Email: "jane@example.com"},
    }, nil
}
```
5. 创建 controller 并集成 GraphQL Handler
```
package controllers

import (
    "github.com/99designs/gqlgen/graphql/handler"
    "github.com/99designs/gqlgen/graphql/handler/transport"
    "github.com/astaxie/beego"
    "myapp/graph"
    "myapp/graph/generated"
)

// GraphQLController 用于处理 GraphQL 请求
type GraphQLController struct {
    beego.Controller
}

func (c *GraphQLController) Post() {
    // 创建 GraphQL 服务器
    srv := handler.NewDefaultServer(generated.NewExecutableSchema(generated.Config{Resolvers: &graph.Resolver{}}))
    srv.AddTransport(transport.POST{})

    // 使用 Beego 的 Context 接口处理 GraphQL 请求
    srv.ServeHTTP(c.Ctx.ResponseWriter, c.Ctx.Request)
}
```
6. 配置 Beego 路由
```
package routers

import (
    "myapp/controllers"
    "github.com/astaxie/beego"
)

func init() {
    // 配置 Beego 路由，直接指向 GraphQLController 的 Post 方法
    beego.Router("/graphql", &controllers.GraphQLController{}, "post:Post")
}

```
7. 测试集成的 GraphQL API
```
curl -X POST -H "Content-Type: application/json" -d '{"query": "{ users { id name email } }"}' http://localhost:8080/graphql
```
8. 可选：GraphQL Playground 集成

如果想在开发过程中使用 GraphQL Playground 进行测试，可以在 controllers/GraphQLController 中添加 Get 方法：
```
func (c *GraphQLController) Get() {
    playground.Handler("GraphQL Playground", "/graphql").ServeHTTP(c.Ctx.ResponseWriter, c.Ctx.Request)
}
```
注册路由：
```
beego.Router("/graphql", &controllers.GraphQLController{}, "get:Get")
```
访问 http://localhost:8080/graphql 时打开 GraphQL Playground，可以在其中交互式地测试 GraphQL API。