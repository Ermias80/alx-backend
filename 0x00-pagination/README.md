# 0x00. Pagination
**Back-end**
## RESOURCES
**Read or watch:**
+ [x] [REST API Design: Pagination](https://intranet.alxswe.com/rltoken/7Kdzi9CH1LdSfNQ4RaJUQw)
+ [x] [HATEOAS](https://intranet.alxswe.com/rltoken/tfzcEbTSdMYSYxsspJH_oA)
## Learning Objectives
At the end of this project, you are expected to be able to explain to anyone, without the help of Google:
* How to paginate a dataset with simple page and page_size parameters
* How to paginate a dataset with hypermedia metadata
* How to paginate in a deletion-resilient manner
## Requirements
* All your files will be interpreted/compiled on `Ubuntu 18.04 LTS using python3 (version 3.7)`
* All your files should end with a new line
* The first line of all your files should be exactly `#!/usr/bin/env python3`
* A `README.md` file, at the root of the folder of the project, is mandatory
* Your code should use the `pycodestyle style (version 2.5.*)`
* The length of your files will be tested using `wc`
* All your modules should have a documentation `(python3 -c 'print(__import__("my_module").__doc__)')`
* All your functions should have a documentation `(python3 -c 'print(__import__("my_module").my_function.__doc__)'`
* A documentation is not a simple word, it’s a real sentence explaining what’s the purpose of the module, class or method (the length of it will be verified)
* All your functions and coroutines must be type-annotated.
## Setup: `Popular_Baby_Names.csv`
[use this data file](https://s3.amazonaws.com/alx-intranet.hbtn.io/uploads/misc/2020/5/7d3576d97e7560ae85135cc214ffe2b3412c51d7.csv?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIARDDGGGOUSBVO6H7D%2F20240818%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20240818T145618Z&X-Amz-Expires=86400&X-Amz-SignedHeaders=host&X-Amz-Signature=e8abbaea0b08580f753fa24f2ef3fef4a1a7da0edc76421ca5c2d5c58b8eb03f "use this data file") for your project
## Tasks
+ [x] 0. **Simple helper function**<br/>[0-simple_helper_function.py](0-simple_helper_function.py) contains a Python function named `index_range` that takes two integer arguments `page` and `page_size` and meets the following requirements:
  + The function should return a tuple of size two containing a start index and an end index corresponding to the range of indexes to return in a list for those particular pagination parameters.
  + Page numbers are 1-indexed, i.e. the first page is page 1.
+ [x] 1. **Simple pagination**<br/>[1-simple_pagination.py](1-simple_pagination.py) contains a Python script that meets the following requirements:
  + Copy `index_range` from the previous task and the following class into your code.
    ```python
    import csv
    import math
    from typing import List
    class Server:
        """Server class to paginate a database of popular baby names.
        """
        DATA_FILE = "Popular_Baby_Names.csv"
        def __init__(self):
            self.__dataset = None
        def dataset(self) -> List[List]:
            """Cached dataset
            """
            if self.__dataset is None:
                with open(self.DATA_FILE) as f:
                    reader = csv.reader(f)
                    dataset = [row for row in reader]
                self.__dataset = dataset[1:]
            return self.__dataset
        def get_page(self, page: int = 1, page_size: int = 10) -> List[List]:
            pass
    ```
  + Implement a method named `get_page` that takes two integer arguments `page` with default value 1 and `page_size` with default value 10.
    + You have to use this [CSV file](Popular_Baby_Names.csv).
    + Use `assert` to verify that both arguments are integers greater than 0.
    + Use `index_range` to find the correct indexes to paginate the dataset correctly and return the appropriate page of the dataset (i.e. the correct list of rows).
    + If the input arguments are out of range for the dataset, an empty list should be returned.
+ [x] 2. **Hypermedia pagination**<br/>[2-hypermedia_pagination.py](2-hypermedia_pagination.py) contains a Python script that meets the following requirements:
  + Replicate code from the previous task.
  + Implement a `get_hyper` method that takes the same arguments (and defaults) as `get_page` and returns a dictionary containing the following key-value pairs:
    + `page_size`: the length of the returned dataset page.
    + `page`: the current page number.
    + `data`: the dataset page (equivalent to the return value from previous task).
    + `next_page`: number of the next page, `None` if no next page.
    + `prev_page`: number of the previous page, `None` if no previous page.
    + `total_pages`: the total number of pages in the dataset as an integer.
  + Make sure to reuse get_page in your implementation.
  + You can use the `math` module if necessary.
+ [x] 3. **Deletion-resilient hypermedia pagination**<br/>[3-hypermedia_del_pagination.py](3-hypermedia_del_pagination.py) contains a Python script that meets the following requirements:
  + The goal here is that if between two queries, certain rows are removed from the dataset, the user does not miss items from dataset when changing page.
  + Start [3-hypermedia_del_pagination.py](3-hypermedia_del_pagination.py) with this code:
    ```python
    #!/usr/bin/env python3
    """
    Deletion-resilient hypermedia pagination
    """
    import csv
    import math
    from typing import List
    class Server:
        """Server class to paginate a database of popular baby names.
        """
        DATA_FILE = "Popular_Baby_Names.csv"
        def __init__(self):
            self.__dataset = None
            self.__indexed_dataset = None
        def dataset(self) -> List[List]:
            """Cached dataset
            """
            if self.__dataset is None:
                with open(self.DATA_FILE) as f:
                    reader = csv.reader(f)
                    dataset = [row for row in reader]
                self.__dataset = dataset[1:]
            return self.__dataset
        def indexed_dataset(self) -> Dict[int, List]:
            """Dataset indexed by sorting position, starting at 0
            """
            if self.__indexed_dataset is None:
                dataset = self.dataset()
                truncated_dataset = dataset[:1000]
                self.__indexed_dataset = {
                    i: dataset[i] for i in range(len(dataset))
                }
            return self.__indexed_dataset
        def get_hyper_index(self, index: int = None, page_size: int = 10) -> Dict:
            pass
    ```
  + Implement a `get_hyper_index` method with two integer arguments: `index` with a `None` default value and `page_size` with default value of 10.
    + The method should return a dictionary with the following key-value pairs:
    + `index`: the current start index of the return page. That is the index of the first item in the current page. For example if requesting page 3 with `page_size` 20, and no data was removed from the dataset, the current index should be 60.
    + `next_index`: the next index to query with. That should be the index of the first item after the last item on the current page.
    + `page_size`: the current page size.
    + `data`: the actual page of the dataset.
  + Use `assert` to verify that `index` is in a valid range.
  + If the user queries index 0, `page_size` 10, they will get rows indexed 0 to 9 included.
  + If they request the next index (10) with `page_size` 10, but rows 3, 6 and 7 were deleted, the user should still receive rows indexed 10 to 19 included.
