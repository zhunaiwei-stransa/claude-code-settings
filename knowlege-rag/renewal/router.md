# code snippet

## router

### code 1

```go
func (s *Server) routerForPsProduct(group *echo.Group) {
	group = group.Group("/ps-products")
	group.GET("/summary", func(c *echo.Context) error {
		transactionManager := rdb.NewTransactionManager(c.Request().Context(), s.db)
		fetchPsProductsUseCase := s.newListPsProductUseCase(transactionManager)
		fetchPsProductsHandler := psproducthandler.NewFetchPsProductsHandler(
			fetchPsProductsUseCase,
		)

		return fetchPsProductsHandler.FetchPsProducts(c)
	})
}
```

## Points and pitfalls

1. var naming style consistence and must-be: transactionManager, fetchPsProductsUseCase, fetchPsProductsHandler