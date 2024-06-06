# Optiver

One of my friend had a OA for Optiver. This was one of the questions he had in the round. He couldn't get the output right. Any one have any idea how to solve this?



In order to improve your beverage making operations you want to create a system for tracking the beverages still needing to be made when the scouts come to update you on their orders.
As part of your system you want to track the following



number_of_stores: The number of stores is the amount of scouts with stores open, where an open store is any scout who currently has a non-zero order size
﻿﻿number_of_orders: The total number of orders for all scouts, noting that an order with 100 quantity is still 1 order
﻿﻿number_of *different* beverages: The number of different beverages that you need to make
• • number_of_beverages: The total number of beverages that you need to make,
seen as the total of all the orders
• number_of_each_beverage: The number of beverages per beverage type across all stores
You will constantly need to maintain that your orders abide to the limits being set, limits can be updated and are defined as
﻿﻿numberOfStores: The total number of stores can can be open
﻿﻿perBeverageTotal: For each beverage, across all stores the total number of beverages that are required
If an order would exceed the limit (when compared with a >) then the order should be ignored and you should print reject_order: ‹uniqueld>
If updating the limit would cause your limits to be exceeded, then you should close all stores, resetting all your orders.
Note: If no limits have been supplied then the limits should all be treated as 0. Scouts will regularly come and update you with what beverages they need, each time they do this it will replace the previous order for the same type of beverage for that scout:



uniqueld: the unique identifier for this order
﻿﻿storeId: the unique identifier for the scouts corner stand
﻿﻿beverageName: the name of the beverage they're updating the order of
﻿﻿quantity: the amount of this beverage that they still require



In addition to this, if a scout's store goes out of business they will tell you their storeld and that all their orders should be cancelled, alternatively, if the total number of beverages for a given scout goes to 0 then that store is also considered out of business.



Note: If an orders size is zero then that order is no longer considered active and does not count towards number_of_orders.
You will need to implement the following functions
﻿﻿UpdateLimits (numberOfStores, perBeverageTotal)
﻿﻿Updates the limits of your business
﻿﻿If on doing so any of the limits are exceeded then you should close all stores
﻿﻿OrderUpdate (storeId, beverageName, quantity)
﻿﻿Updates the given stores order for a given beverage name
﻿﻿prints "reject_order: ‹uniqueld> in the case that it exceeds any of the limits
﻿﻿CloseStore (storeId)
﻿﻿Closes the given store removing any orders
﻿﻿PrintState()
﻿﻿prints the current state in the format "number_of_stores:
‹number_of_stores>, number_of_orders: ‹number_of_orders›, number_of_different_beverages:
‹number_of_different_beverages›, number_of_beverages:
number_of.
_beverages>"

```C++
#include <cstdint>
#include <exception>
#include <map>
#include <set>
#include <list>
#include <cmath>
#include <ctime>
#include <deque>
#include <queue>
#include <stack>
#include <stdexcept>
#include <string>
#include <bitset>
#include <cstdio>
#include <limits>
#include <vector>
#include <climits>
#include <cstring>
#include <cstdlib>
#include <fstream>
#include <numeric>
#include <sstream>
#include <iostream>
#include <optional>
#include <algorithm>
#include <unordered_map>

using namespace std;
class OrderState
{
public:
    int number_of_stores = 0;
    int number_of_orders = 0;
    int number_of_different_beverages = 0;
    int number_of_beverages = 0;
    // int number_of_each_beverage = 0;
    int numberOfStores = 0;
    int perBeverageTotal = 0;
    // int orderCnt = 0;
    // set<int> store_set;
    // unordered_map <string, int> beverages_count; // 关店时，该beverage - 1。 店新增饮品时，该beverage + 1。 用于计算  number_of_different_beverages。
    // unordered_map<string, int> beverages_num_count;
    unordered_map<int, unordered_map<string, int>> beverages;
    
    OrderState()
    {
        /* Your code here */
        // beverages[1]["happy"] = 1;
        
    }

    void UpdateLimit(const int numberOfStores2, const int perBeverageTotal2)
    {
        /* Your code here */
        numberOfStores = numberOfStores2;
        perBeverageTotal = perBeverageTotal2;
        
    }

    void ProcessOrder(const int uniqueId, const int storeId, const std::string& beverageName, const int quantity)
    {
        /* Your code here */
        // number_of_orders ++;
        if (beverages[storeId][beverageName] == 0) {
            number_of_orders++;
        }
        if ((number_of_stores == numberOfStores && !store_set.count(storeId) || 
                beverages_num_count[beverageName] - beverages[storeId][beverageName] + quantity > perBeverageTotal)) {
            cout << "reject_order: " << uniqueId << endl;
            return;
        }
        beverages_num_count[beverageName] = beverages_num_count[beverageName] - beverages[storeId][beverageName] + quantity;
        if (!store_set.count(storeId)) {
            number_of_stores++;
            store_set.insert(storeId);  
        }
        if (beverages[storeId][beverageName] == 0) {
            if (beverages_count[beverageName] == 0)
                number_of_different_beverages += 1;
            beverages_count[beverageName]++;
        }
        number_of_beverages -= beverages[storeId][beverageName];
        beverages[storeId][beverageName] = quantity;
        number_of_beverages += quantity;
        return;  
    }

    void CloseStore(const int storeId)
    {
        for (auto [beverage, cnt]: beverages[storeId]) {
            if (--beverages_count[beverage] == 0)
                number_of_different_beverages--;
            number_of_beverages -= cnt;
            beverages_num_count[beverage] -= cnt;
        }
        beverages[storeId].clear();
        number_of_stores--;
        return;
    }

    void PrintState()
    {
        cout << "number_of_stores: " << number_of_stores 
            << ", number_of_orders: " << number_of_orders
            << ", number_of_different_beverages: " << number_of_different_beverages
            << ", number_of_beverages: " << number_of_beverages << endl;
        
        /* Your code here */
    }
};


int main()
{
    OrderState OrderState{};

    std::string operation;
    int numberOfStores;
    int perBeverageTotal;
    int uniqueId;
    int storeId;
    std::string beverage;
    int quantity;
    
    while(!std::cin.eof())
    {
        std::cin >> operation;
        if (operation == "UPDATE_LIMIT")
        {
            std::cin >> numberOfStores >> perBeverageTotal;
            OrderState.UpdateLimit(numberOfStores, perBeverageTotal);
        }
        else if (operation == "ORDER_UPDATE")
        {
            std::cin >> uniqueId >> storeId >> beverage >> quantity;
            OrderState.ProcessOrder(uniqueId, storeId, beverage, quantity);
        }
        else if (operation == "CLOSE_STORE")
        {
            std::cin >> storeId;
            OrderState.CloseStore(storeId);
        }
        else if (operation == "PRINT_STATE")
        {
            OrderState.PrintState();
        }
        else
        {
            std::cerr << "Malformed input!\n" << operation << std::endl;
            return -1;
        }
        if (std::cin.eof()) break;
    }
    return 0;
}
```



A friend asks you to tune into a lecal numbers station, a radio station that is publishing a coded message. They say they've had a tip on how to decipher the messages the station is publishing, but want your help in writing a program that will do the deciphering. You start listening to the station and notice that in each voice sample the person reads out a number follow by letter or a hyphen '-'.
The numbers don't seem to be in any particular order...



Every sample will contain a number which can be used to put the messages in order (sequerice (D).



- Every message only contains the letters a-z.
- A complete message is a continuous set of characters surrounded by two - characters.
- Sometimes a message is never completed (missing sequence IDs).
- Once you've completed a full message, all messages completed later with a lower sequence iD are no longer useful and shouldnt be output (you should only output the latest message).
- if two full messages are completed at the same time, then you should output only the message containing higher sequence IDS.
- A full broadcast can contain multiple complete messages ano you will need to output each of them.



**Function Description**



You are to implernent the function Processsample that takes:



- a sequence number for the sample,
- the character for the sample.
  Whenever a full message is completed you should call
  the OnMessageComplete function with the completed message.



**Constraints**



- 

  0 < sequence id <= 2^64 - 1

  

- 

  number of messages 5 * 10^5

  



**Input Format**



For each line, the first string is the sequense number associated with
the sample, the second character is the character related to the
sequence number



**Sample Case 0**
Sample input



```
1 -
2 h
3 e
4 y
5 -
6 b
7 -
```



Sample Output
`hey`



**Sample Case 1**
Sample input



```
1 -
2 h
3 e
5 -
6 b
7 y
8 e
9 -
4 y
```



Sample Output
`bye`



Boiler-plate code



```cpp
#include<bits/stdc++.h>
using namespace std;

class IPrinter
{
public:
    virtual ~IPrinter() = default;
    virtual void OnMessageComplete(const std::string &message) const = 0;
};

class Printer
    : public IPrinter
{
public:
    void OnMessageComplete(const std::string &message) const override
    {
        std::cout << message << std::endl;
    }
};

class Decoder
{
public:
    Decoder(const IPrinter &printer)
        : mPrinter(printer)
    {
    }

    void ProcessSample(const uint64_t sequence, const char character)
    {
        /* complete your function and call mPrinter.OnMessageComplete when completing a message */
    }

    const IPrinter &mPrinter;
};

int main()
{
    Printer printer;
    Decoder decoder(printer);

    uint64_t seqNum;
    char message;

    while (!std::cin.eof())
    {
        std::cin >> seqNum >> message;
        if (std::cin.eof())
            break;
        decoder.ProcessSample(seqNum, message);
    }

    return 0;
}
```
