// SPDX-License-Identifier: MIT
pragma solidity ^0.8.17;

contract declaration {

address owner;
string public Branch_name;

struct Product_info{
    string Product_Name;
    string condition;
    address Seller;
    uint Amount;
    uint Quantity;
    uint Date;
    string Location;
    bool Sold;
}
mapping (uint Product_number => Product_info) public Listed_items;

struct info{
    uint[] Customer_id;
    string[] item_name;
    uint[] amount;
    uint[] Date;
    string[] Location;
    uint[] quantity;

}
info[] _Customer_details;
mapping (address => info)  customer_Detail;

uint public Product_Number;
uint Customer_id;

constructor(string memory Branch_Name) {
    owner = msg.sender;
    Branch_name = Branch_Name;
    Product_Number = 1;
}
modifier onlyowner(){
     require(msg.sender == owner, "only owner can be list this" );
    _;
}

function Add_item(string memory Product_name ,string memory _Condition ,uint _quantity ,  string memory _Location , uint price) external onlyowner returns(bool){
 Product_info storage temp = Listed_items[Product_Number];
 temp.Seller = msg.sender;
 temp.Product_Name = Product_name;
 temp.condition = _Condition;
 temp.Location = _Location;
 temp.Quantity = _quantity;
 temp.Amount = price;
 temp.Date = block.timestamp;
 temp.Sold = false;
 Product_Number++;
return true;
}
function Update_Details(uint Deal_no , string memory Product_name, uint _quantity, string memory _Condition , string memory _Location ,  uint price , bool Is_sold) external onlyowner returns(bool){
 Product_info storage temp = Listed_items[Deal_no];
 temp.Product_Name = Product_name;
 temp.Quantity = _quantity;
 temp.condition = _Condition;
 temp.Location = _Location;
 temp.Amount = price;
 temp.Sold = Is_sold;

return true;
}

function buy_item(uint Product_no , uint quantity , string memory Location ) external payable returns (bool){
 require(quantity > 0 , "minimum 1 or more");
 require(Listed_items[Product_no].Quantity > quantity  , "Sorry But this Quantity Not avalible");

uint amount = quantity * Listed_items[Product_no].Amount;
   require(msg.value >= amount , "insufficeint balance");

payable (owner).transfer(msg.value);
 Listed_items[Product_no].Quantity -= quantity ;
 if(Listed_items[Product_no].Quantity == 0){
    Listed_items[Product_no].Sold = true ;
 }
  info storage temp = customer_Detail[msg.sender];
  temp.Customer_id.push(Customer_id) ;
  temp.item_name.push(Listed_items[Product_no].Product_Name);
  temp.amount.push(amount);
  temp.Date.push(block.timestamp);
  temp.Location.push(Location);
  temp.quantity.push(quantity);
  Customer_id++;
 _Customer_details.push(temp);
  return true;
}

function Check_Customer_details(address _addr) view external returns (info memory){
 return customer_Detail[_addr];
}

}
