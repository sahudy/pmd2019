
# Exercícios - Soluções
- 1 -  `from Employees select FirstName`
- 2 -  `from Companies where Address.Cselect Name`
- 3 - `from Companies 
where (Address.City = "Campinas" or Address.City = "Sao Paulo")
Country = "Brazil" select Name, Address`
- 4 - `from Orders
where Freight BETWEEN 50 and 100`
- 5 - `from Orders
select ShipTo, Lines[].ProductName as Products`
- 6 - `declare function lineItemPrice(l) {
    return l.PricePerUnit * l.Quantity * (1 - l.Discount);
}
from Orders as o
select {
    TopProducts: o.Lines
    Total: o.Lines.reduce((acc, l) => acc + lineItemPrice(l), 0)
}`
- 7 -  `from Orders as o
where Company = 'companies/88-A'
load o.Employee as e, o.Company as c, o.Lines[].Product as products[]
select { 
    CompanyName: c.Name,
    EmployeeName: e.FirstName + " " + e.LastName,
    Products: products
}`
- 8 - `from Companies
order by Name as asc`
- 9 - `from Products 
order by PricePerUnit as double desc`
- 10 - `from Companies
where spatial.within(
    spatial.point(Address.Location.Latitude, Address.Location.Longitude), 
    spatial.circle(500, -22.9306303, -43.358808, 'kilometers'))`
- 11 - `from Products
group by Supplier
where count() >= 3
select Supplier, count()`
> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTQ0ODY0ODIzOV19
-->