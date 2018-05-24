# Final-Project---CIS-17A

//Eduardo Sanchez, Manuel Acevedo    
//CIS-17A
//PROJECT - Final version


#include <iostream>
#include<string>
#include<fstream>
#include <iomanip>
#include <memory>
using namespace std;
/*****************************************
 * Initialize Global Variables
 * **************************************/
const int NUM=6;
const float JAN_JUNE= .05;
const float JULY_DEC = .10;
const float STATE_TAX= .08;
const float SAN_D = .05;
const float LOS_A = .08;
const float RIVER_S = .03;
/*******************************************
 Function Prototypes
 ******************************************/
void userInformation(int *months, float userID, float *weight, 
                    string *county, float *amountSpent, string counties[]);

void calcDiscount(int *months, float *amountSpent, float *discount);

void calcStateTax(float *amountSpent, float *discount, float *statetax);

void calcCountyTax(float *amountSpent, float *discount, float *countyTax, string *county);

void calcTotal(float *amountSpent,float *discount, float *countyTax, float *statetax, float *totalPrice);

void recieptToFile(ofstream &outputFile, float *amountSpent, float *countyTax, float *discount, float *statetax, float *totalPrice);

 void sortArray(string counties[]);
 
 int LinearSearch(string counties[], string *county);

/******************************************
 * Main function
 * ***************************************/
int main()
{

  string counties[NUM]={"San Diego","Los Angeles","Riverside", "san diego", "los angeles", "riverside"};
  
  // Variable Initiation 
  int months;
  float userID;
  unique_ptr<float>userIdPtr(new float);
  //userIdPtr=userID;
  float weight;
  string county;
  float amountSpent;
  float discount;
  float countyTax;
  float statetax;
  float totalPrice;
  ofstream outputFile;
  
  // Function Call Statements
  outputFile.open("outputFile.txt");    
  
  sortArray(counties);

  userInformation(&months, userID, &weight, &county, &amountSpent, counties);
  cout << endl << endl;
  
  calcDiscount(&months, &amountSpent, &discount);
  calcStateTax(&amountSpent, &discount, &statetax);
  calcCountyTax(&amountSpent, &discount, &countyTax, &county);
  calcTotal(&amountSpent, &discount, &countyTax, &statetax, &totalPrice);
  
  /***********************************************
   * formatting the values that are to be displayed
   * *********************************************/
  
  cout << fixed << showpoint << setprecision(2);
  cout << endl << "Subtotal: " << "$" << amountSpent << endl;
  cout << endl << endl;
  cout << "Additional charges/discounts:\n";
  cout << setw(20) << "------------------------------\n";
  
  cout << setw(20)<<left<< "Discount: " << "$"<< discount;
  cout << endl;
  cout << setw(20)<<left<< "State Tax: " << "$"<< statetax;
  cout << endl;
  cout << setw(20)<<left<< "County Tax: " << "$"<< countyTax;
  cout << endl << endl;
  cout << setw(20) <<"------------------------------\n";
  cout << endl;
  cout <<setw(20)<< "TOTAL: "<< "$" <<totalPrice;
  
  
  recieptToFile(outputFile, &amountSpent,&countyTax, &discount, &statetax, &totalPrice);
  cout << endl << endl << "Your reciept was sent to a file. Thank you for using our program! Goodbye.";
  
}
/***********************************************************************
 * The function userInformation displays instructions for user input. Also,
 * it validates all user input so that irrevelant input is disregarded.
 **********************************************************************/
 void userInformation(int *months, float userID, float *weight, string *county, float *amountSpent, string counties[])
 {
   char cont;
   
  cout << "This program allows the user to enter in input and prints out a reciept.\n\n";
  cout <<"Please enter in your ID: ";
  cin >> userID;
    
    while(userID<=0)
    {
     cout << "That is an invalid Identificaton\n";
     cout <<"Please enter in your ID: ";
     cin >> userID;
    }
  
  cout << "Please enter in the the month you purchased your items: ";
  cin >> *months;
    
    while(*months<0 || *months>12)
    {
     cout << endl;
     cout << "That is an invalid month.\n";
     cout <<"Please enter in the month you purchased your items: ";
     cin >> *months;
    }
  
  cout << "Enter in cart sub-total: ";
  cin >>*amountSpent;
    
    while(*amountSpent<0)
    {
      cout <<"That is an invalid sub-total, Enter in amount spent: ";
      cin >> *amountSpent;
    }
  
  cout << endl << endl;
  cout << "Here are the counties that we ship to: ";
      
      for(int x=0; x<3; x++)
      {
        cout << endl << (x+1) <<". " << counties[x] << endl;
      }
  
  cout << endl;
  cin.ignore();
  cout << "Enter in the county you live in: ";
  getline(cin, *county);
  cout << endl << endl;
 int x= LinearSearch(counties, county);
    while(x==-1)
    {
    cout << "That is an invalid county, enter in the county you live in: ";
    getline(cin, *county);
    x= LinearSearch(counties, county);

    }
    switch(x)
    {
      case 2:
      case 5: 
          cout << "Due to shipping distance, shipping to San Diego requires a 5% extra charge.";
          break;
      case 0:
      case 3:
          cout << "Due to shipping distance, shipping to Los Angeles requires a 8% extra charge";
          break;
      case 1:
      case 4:
          cout << "Due to shipping distance, shipping to Riverside requires a 3% extra charge";
          break;
    }
    
    /********************************************
     * Inlcuded an exit function incase user deos not want to continue with the purchase.
     * *******************************************/
    
  cout << endl << "Would you like to continue use Y for yes or N for no?: ";
  cin >> cont;
      if (cont == 'N'||cont== 'n')
          exit(0);
  

}

/********************************************************************************
 * The function calcDiscount calculates the discount that the user will recieve based upon the month they are purchasing the item.
 * The discounts are:
 * -------------------
 * Jan - June = 5%
 * July - Dec = 10%
 *******************************************************************************/
void calcDiscount(int *months, float *amountSpent, float *discount)
{
  if(*months<=6)
  {
    *discount= *amountSpent*JAN_JUNE;
  }
  else
  {
    *discount= *amountSpent*JULY_DEC;
  }
  
}
/********************************************************************************
 * The function calcStateTax calculates the state tax.
 * The state tax is 8%
 * ******************************************************************************/
void calcStateTax(float *amountSpent, float *discount, float *statetax)
{
  *statetax= (*amountSpent-*discount)*STATE_TAX;
  

}
/************************************************************
 * The function calcCountyTax calculates the county tax depending on which county the user selected.
 ***********************************************************/

void calcCountyTax(float *amountSpent, float *discount, float *countyTax, string *county)
{
  if(*county == "San Diego" || *county == "san diego")
  *countyTax = (*amountSpent-*discount)* SAN_D;
  
  else if(*county == "Los Angeles" || *county == "los angeles")
  *countyTax = (*amountSpent-*discount)*LOS_A;
  
  else 
  *countyTax= (*amountSpent-*discount)*RIVER_S;
  
  
}

/************************************************************
 * The function calcToatal calculates the total price that the user will be charged.
 ************************************************************/
 
 void calcTotal(float *amountSpent,float *discount, float *countyTax,float *statetax, float *totalPrice)
 {
   *totalPrice = (*amountSpent - *discount + *countyTax + *statetax);
 }
/*************************************************************
 * The function recieptToFile outputs the reciept to the file.
 ************************************************************/
 void recieptToFile(ofstream &outputFile, float *amountSpent, float *countyTax, float *discount, float *statetax, float *totalPrice)
 {
  outputFile << endl << endl;
  outputFile<< "RECEIPT:\n";
  outputFile<< setw(20) << "------------------------------\n";
  outputFile << fixed << showpoint << setprecision(2);
  outputFile << endl <<setw(20)<< left << "Subtotal: " << "$" << *amountSpent << endl;
  outputFile << setw(20)<<left<< "Discount: " << "$"<< *discount;
  outputFile << endl;
  outputFile << setw(20)<<left<< "State Tax: " << "$"<< *statetax;
  outputFile << endl;
  outputFile << setw(20)<<left<< "County Tax: " << "$"<<*countyTax;
  outputFile << endl << endl;
  outputFile << setw(20) <<"------------------------------\n";
  outputFile << endl;
  outputFile <<setw(20)<< "TOTAL: "<< "$" << *totalPrice;
 }
 /*************************************************
 ** The sortArray function will sort the string array with counties based on the first letter. 
 NOTE: Uppercase and lowercase characters of the same letter are not equivalent when being sorted.
 **************************************************/
 void sortArray(string counties[])
 {
   int maxElement;
   int index;
   
   for(maxElement = NUM-1; maxElement >0; maxElement--)
    {
      for (index = 0; index < maxElement; index++)
      {
          if(counties[index]> counties[index+1])
          {
            swap(counties[index], counties[index+1]);
          }
      }
    }
 }
 
 /****************************************************
 ** LinearSearch function will conduct a linear search for ** the county that the user enters.
 *******************************************************/
 int LinearSearch(string counties[], string *county)
 {
 int index=0;
 int position=-1;
 bool found = false;

 while(index<NUM && !found)
{
  if (counties[index]==*county)
  {
    found = true;
    position=index;
  }
  index++;
}
return position;
 }
