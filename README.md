# tools

## enum2func

*enum2func* generates functions for enums.

For example, we define an enum as follows:

```go
type ctxKey byte

//go:generate enum2func -c ./enum2func.yml
const (
	ctxKeyCompanyInfo ctxKey = iota // 公司信息
	ctxKeyEnvInfo                   // 环境信息
)
```

And configure enum2func.yml as follows:

```yaml
tasks:
    - type: ctxKey
      input: ./context.go
      output: ""
      trimPrefix: "ctxKey"
      package: ""
      imports:
        - '"context"'
        - ''
        - 'v1 "github.com/zhijingtech/api/v1"'
      lines:
        - ''
        - 'func (ctx *Context) Get{{.TrimmedName}}() (*{{.TrimmedName}}, error) {'
        - '  info, ok := ctx.Value({{.Name}}).(*{{.TrimmedName}})'
        - '  if !ok {'
        - '    return nil, v1.ErrorNotFound("{{.TrimmedName}} not found")'
        - '  }'
        - '  return info, nil'
        - '}'
        - ''
        - 'func (ctx *Context) Set{{.TrimmedName}}(info *{{.TrimmedName}})  {'
        - '	ctx.Context = context.WithValue(ctx.Context, {{.Name}}, info)'
        - '}'
```

When running `go generate ./...`, the following code will be generated in ctxkey_func_gen.go:

```go
// Code generated by "enum2func"; !!!DO NOT EDIT!!!
package biz

import (
	"context"

	v1 "github.com/zhijingtech/api/v1"
)

func (ctx *Context) GetCompanyInfo() (*CompanyInfo, error) {
	info, ok := ctx.Value(ctxKeyCompanyInfo).(*CompanyInfo)
	if !ok {
		return nil, v1.ErrorNotFound("CompanyInfo not found")
	}
	return info, nil
}

func (ctx *Context) SetCompanyInfo(info *CompanyInfo) {
	ctx.Context = context.WithValue(ctx.Context, ctxKeyCompanyInfo, info)
}

func (ctx *Context) GetEnvInfo() (*EnvInfo, error) {
	info, ok := ctx.Value(ctxKeyEnvInfo).(*EnvInfo)
	if !ok {
		return nil, v1.ErrorNotFound("EnvInfo not found")
	}
	return info, nil
}

func (ctx *Context) SetEnvInfo(info *EnvInfo) {
	ctx.Context = context.WithValue(ctx.Context, ctxKeyEnvInfo, info)
}
```
