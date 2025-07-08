## restituisce i prodotti di una determinata categoria
```
CREATE PROCEDURE GetProductsByCategory
    @CategoryID INT
AS
BEGIN
    SELECT * FROM Products
    WHERE CategoryID = @CategoryID;
END;

EXEC GetProductsByCategory @CategoryID = 2;
```

## inserisce un nuovo cliente
```
CREATE PROCEDURE AddCustomer
    @FirstName NVARCHAR(50),
    @LastName NVARCHAR(50),
    @Email NVARCHAR(100)
AS
BEGIN
    INSERT INTO Customers (FirstName, LastName, Email)
    VALUES (@FirstName, @LastName, @Email);
END;

EXEC AddCustomer
    @FirstName = 'Mario',
    @LastName = 'Rossi',
    @Email = 'mario.rossi@example.com';
```

## restituisce il numero di prodotti disponibili
```
CREATE PROCEDURE GetProductCount
    @Count INT OUTPUT
AS
BEGIN
    SELECT @Count = COUNT(*) FROM Products;
END;

DECLARE @Total INT;
EXEC GetProductCount @Count = @Total OUTPUT;
PRINT 'Totale prodotti: ' + CAST(@Total AS VARCHAR);
```

## controlla se un prodotto esiste in base all’ID
```
CREATE PROCEDURE CheckProductExists
    @ProductID INT
AS
BEGIN
    IF EXISTS (SELECT 1 FROM Products WHERE ProductID = @ProductID)
        PRINT 'Il prodotto esiste.';
    ELSE
        PRINT 'Prodotto non trovato.';
END;

EXEC CheckProductExists @ProductID = 10;
```
