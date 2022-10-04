<center style="font-weight: bold; font-size: 25 "> ADA CheatSheet</center>

## Tipos de Datos

#### Simples

**Integer** -> -999 .. 999

**Float** -> -999.999 .. 999.999

**Natural** -> 0 .. 999

**Character** -> 'a', 'b' ...

**String** -> "String of characters"

**Boolean** -> true / false

#### Complejos

#### Record:

``` ADA
type T_Name is 
record
	V_Name: Integer;
	V_Name: String;
end record;

#### Procedure
``` ADA
procedure name (param:in Type) is
declare --optional
	-- declarations
begin
	-- implementation
end name;
```

#### Task

``` ADA
-- Task type:

task type T_name

-- Anonimus Task

task T_name
```

## Estructuras

#### Function
``` ADA
function name (param:Type) return Type is
declare --optional
	-- declarations
begin
	-- implementation
return variable;
end name;
```

#### Package
``` ADA
-------file.ads-------
package name is
	-- public declarations
private
	-- private declarations
end name;

-------file.adb-------
package body name is
	-- inits
begin
	-- implementation
end name;
```

#### Generic

``` ADA
generic
	-- Generic Variables
	type T_name is private
	
	procedure P_name (V_name: in out T_name; V_name: in out T_name);

-- Use generic Procedure
Procedure new_P_name is new P_name(T_name => Int);

```

#### Task

``` ADA
-------file.ads-------
Task type name (P_name) is
	-- public declarations
private
	-- private declarations
end name;

-------file.adb-------
task body name is
	-- inits
begin
	-- implementation
end name;
```



