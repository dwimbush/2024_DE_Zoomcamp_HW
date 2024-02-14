# 2024_Data_Engineering_Zoomcamp_Workshop 1 Homework
Repository for 2024 Data Engineering Zoomcamp Homework

## Week 3 dlt Workshop

### Question 1: What is the sum of the outputs of the generator for limit = 5?
```PYTHON
def square_root_generator(limit):
    n = 1
    while n <= limit:
        yield n ** 0.5
        n += 1

# Example usage:
limit = 5
generator = square_root_generator(limit)

sum_of_squares = 0  # Initialize sum
for sqrt_value in generator:
    print(sqrt_value)
    sum_of_squares += sqrt_value  # Add each square root to the sum

# After the loop, print the total sum
print("Sum of the square roots:", sum_of_squares) 
```
### Question 2: What is the 13th number yielded by the generator?
```PYTHON
def square_root_generator(limit):
    n = 1
    while n <= limit:
        yield n ** 0.5
        n += 1

# To get the 13th number:
limit = 13  # Ensure the limit is at least 13
generator = square_root_generator(limit)

thirteenth_sqrt = None
for i in range(13):  # Loop exactly 13 times
    thirteenth_sqrt = next(generator, None)

if thirteenth_sqrt is not None:
    print("The 13th square root is:", thirteenth_sqrt)
else:
    print("The generator did not produce 13 numbers.")
```

### Question 3: Append the 2 generators. After correctly appending the data, calculate the sum of all ages of people.
```BASH
#Install the dependencies
%%capture
!pip install dlt[duckdb]
```

```PYTHON
# two generator functions defined
def people_1():
    for i in range(1, 6):
        yield {"ID": i, "Name": f"Person_{i}", "Age": 25 + i, "City": "City_A"}

for person in people_1():
    print(person)


def people_2():
    for i in range(3, 9):
        yield {"ID": i, "Name": f"Person_{i}", "Age": 30 + i, "City": "City_B", "Occupation": f"Job_{i}"}


for person in people_2():
    print(person)

import dlt

# define the connection to load to using duckdb
generators_pipeline = dlt.pipeline(destination='duckdb', dataset_name='generators')

# we can load any generator to a table at the pipeline destination as follows:
info = generators_pipeline.run(people_1(),
										table_name="people_download",
										write_disposition="replace")

# the outcome metadata is returned by the load and we can inspect it by printing it.
print(info)

# we can load the next generator to the same or to a different table.
info = generators_pipeline.run(people_2(),
										table_name="people_download",
										write_disposition="append")
print(info)

# show outcome

import duckdb

conn = duckdb.connect(f"{generators_pipeline.pipeline_name}.duckdb")

# let's see the tables
conn.sql(f"SET search_path = '{generators_pipeline.dataset_name}'")
print('Loaded tables: ')
display(conn.sql("show tables"))

# and the data

print("\n\n\n people_download table below:")

data = conn.sql("SELECT * FROM people_download").df()
display(data)

# sum of all ages in table
age_sum = conn.sql("SELECT sum(age) FROM people_download").df()
display(age_sum)
```

### Question 4: Merge the 2 generators using the ID column. Calculate the sum of ages of all the people loaded as described above.
```PYTHON
# two generator functions defined
def people_1():
    for i in range(1, 6):
        yield {"ID": i, "Name": f"Person_{i}", "Age": 25 + i, "City": "City_A"}

for person in people_1():
    print(person)


def people_2():
    for i in range(3, 9):
        yield {"ID": i, "Name": f"Person_{i}", "Age": 30 + i, "City": "City_B", "Occupation": f"Job_{i}"}


for person in people_2():
    print(person)

import dlt

# define the connection to load to using duckdb
generators_pipeline = dlt.pipeline(destination='duckdb', dataset_name='generators')

# we can load any generator to a table at the pipeline destination as follows:
info = generators_pipeline.run(people_1(),
										table_name="people_download2",
										write_disposition="replace",
                               )

# the outcome metadata is returned by the load and we can inspect it by printing it.
print(info)

# we can load the next generator to the same or to a different table.
info = generators_pipeline.run(people_2(),
										table_name="people_download2",
										write_disposition="merge",
                    primary_key="ID")
print(info)

# show outcome

import duckdb

conn = duckdb.connect(f"{generators_pipeline.pipeline_name}.duckdb")

# let's see the tables
conn.sql(f"SET search_path = '{generators_pipeline.dataset_name}'")
print('Loaded tables: ')
display(conn.sql("show tables"))

# and the data

print("\n\n\n people_download2 table below:")

data = conn.sql("SELECT * FROM people_download2").df()
display(data)

# sum of all ages in table
age_sum = conn.sql("SELECT sum(age) FROM people_download2").df()
display(age_sum)
```

