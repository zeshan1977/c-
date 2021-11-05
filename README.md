# c
 #include <stdio.h>
#include <string>
#include <sstream>
#include <math.h>
#include <time.h>
#include <ctime>
#include <string.h>
#include <cstdlib>

using namespace std;

int subtractSecondsFromTime( int, int);
//int getSecondsBetweenTime ( int,int );
//int addSecondsToTime(int,int );
int subtractSecondsFromTime(int ,int );
int addSecondsToTime(int ,int );
int copychar (const char* , char* );

void getPriceDirection(char *p);
double getRounded(double);
int    getRounded1(int);

int EQ_double(double , double );
int GT_double(double , double );
int LT_double(double , double );
double chopPrecision (double);

void getSmartExpStr(string );

string getExpStr(int,double);

template<class I>
string ToString(const I&);

/********/
const int POINTS= 25; //from 0 to 25 = 26 ( 9:30 = OPEN, 945,1000 .... 1545)
typedef struct {
 double priceCurve[POINTS];
 int index;
 int first;
} DATAPOINTS2;

DATAPOINTS2* ptrDataPoint2;

typedef struct {
 int qty;
 double price;
 double notional;
} SMART_EXP_STR;

SMART_EXP_STR* smartExpStr;

double roundPrice(string , double );
double *  testPtrReturn();
string& testStrRefRtrn();

/********/
class A {
public:
 A() {
  printf ("In Ctor of A \n");
 }

};
/********/
class MZSingleton {

public:
 static MZSingleton* getMZSingleton1();
 static MZSingleton* getMZSingleton2();
protected:
 MZSingleton();
 MZSingleton(const MZSingleton&);
 MZSingleton& operator=(const MZSingleton&);
private:
 static MZSingleton* MZSingleton_ptr;

};


MZSingleton* MZSingleton::MZSingleton_ptr=0;// initialize pointer

MZSingleton* MZSingleton::getMZSingleton1()
{
 if (MZSingleton_ptr == 0)  // is it the first call?
 {
  MZSingleton_ptr = new MZSingleton; // create sole instance
 }
 return MZSingleton_ptr; // address of sole instance
}
MZSingleton* MZSingleton::getMZSingleton2()
{
 static MZSingleton MZSingleton_ref;
 return &MZSingleton_ref;
}
MZSingleton::MZSingleton()
{

}


/********/
/*
string& testStrRefRtrn() {
string  &strt123;
strt123 = new string("foo");
return strt123;
}
*/
double* testPtrReturn() {
 double fo = 23.45;

 double* p=&fo;


 return p;

 //      double w      = 880.24;
 //      double *ws    = &w;
 //      double **wwss = &ws;

 //      return wwss;

}

string getExpStr(int qty, double price ) {

 double notional = qty*price;

 char str[50];

 //sprintf_s(str,"%-20.4f",notional);
 sprintf(str,"%-20.4f",notional);
 

 string retstr;
 retstr =  ToString<int>(qty);
 retstr += "@$";
 retstr += ToString<double>(price);
 retstr += "=$";
 //retstr += ToString<double>(notional);
 retstr += str;

 return retstr;

}
template<class T>
std::string ToString(const T& val)
{
 std::stringstream strm;
 strm << val;
 return strm.str();
}

void setSmartExpStr(SMART_EXP_STR* sPtr,int qty,double px,double notional) {

 sPtr = (SMART_EXP_STR*) malloc(sizeof(*sPtr));

 sPtr->qty=qty;
 sPtr->price=px;
 sPtr->notional=notional;

}

//SMART_EXP_STR* getSmartExpStr(string smartExpStr) {

void getSmartExpStr(string smartExpStr) {
 //char stet[]="152345-152623";
 //
 //
 //

 //int len = strlen(smartExpStr.c_str());
 char *stet= const_cast<char *> (smartExpStr.c_str());
 char * ptr_stet;

 printf("test29 strlen stet %d \n",strlen(stet));
 ptr_stet = strtok(stet,"@");
 printf("test29 Qty:%d \n",atoi(ptr_stet));
 ptr_stet = strtok(NULL,"@");
 printf("test29 px:%f \n",atoi(ptr_stet));

}

double roundPrice(string side, double price){
 double returnPrice = -1;
 if (side=="buy"){
  double tmp1 = 100*price;
  int tmp2 = (int)tmp1;
  double buying = (double)(tmp2)/100;
  returnPrice = buying;
 }else{
  double tmp3 = price + 0.009;
  double tmp4 = 100*tmp3;
  int tmp5 = (int)tmp4;
  double selling = ((double)tmp5)/100;
  returnPrice = selling;
 }
 return returnPrice;
}

int subtractSecondsFromTime(int time1,int secs){
 int temp = time1/10000*3600  + (time1%10000)/100*60 + time1%100;
 temp-=secs;
 return temp/3600*10000  +   temp%3600/60*100 +  temp%60;
}

int addSecondsToTime(int time1,int secs){
 int temp = time1/10000*3600+  (time1%10000)/100*60 + time1%100;
 temp+=secs;
 return temp/3600*10000+temp%3600/60*100+temp%60;
}

/*
// pass in 123425,29 (will subtract 29 seconds from 123425)
// return 123356
int subtractSecondsFromTime( int timeHHMMSS,int seconds) {
time_t now;
time(&now);
struct tm * currentTimeNow = localtime(&now);
mktime(currentTimeNow);

char strtimeHHMMSS[6];
sprintf(strtimeHHMMSS,"%d",timeHHMMSS);

char hour[2];
char min[2];
char sec[2];

hour[0]=strtimeHHMMSS[0];
hour[1]=strtimeHHMMSS[1];
hour[2] ='\0';
int hourint = atoi(hour);

if (hourint == 0 || hourint > 23 || hourint <10) {
return -1;
}

min[0]=strtimeHHMMSS[2];
min[1]=strtimeHHMMSS[3];
min[2] ='\0';
int minint = atoi(min);
if (minint <0  || minint > 59) {
return -1;
}

sec[0]=strtimeHHMMSS[4];
sec[1]=strtimeHHMMSS[5];
sec[2] ='\0';
int secint = atoi(sec);
if (secint <0  || secint > 59) {
return -1;
}


tm newTime = tm();
newTime.tm_sec=secint;
newTime.tm_min=minint;
newTime.tm_hour=hourint;
newTime.tm_mday=currentTimeNow->tm_mday;
newTime.tm_mon= currentTimeNow->tm_mon;
newTime.tm_year=currentTimeNow->tm_year;

time_t myTimeT = mktime(&newTime);
newTime.tm_sec = newTime.tm_sec - seconds;
newTime.tm_min = newTime.tm_min;
mktime(&newTime);

printf ("tm_min tim_min %d tim_min*100 %d \n",newTime.tm_min,newTime.tm_min*100);
int timeMinusSeconds =newTime.tm_hour*10000 +  newTime.tm_min*100 + newTime.tm_sec;
}
*/

// pass in 123356,123429
// return 34
int getSecondsBetweenTime ( int time1HHMMSS,int time2HHMMSS) {
 time_t now;
 time(&now);
 struct tm * currentTimeNow = localtime(&now);
 mktime(currentTimeNow);

 char strtimeHHMMSS[6];
 sprintf(strtimeHHMMSS,"%d",time1HHMMSS);

 char hour[2];
 char min[2];
 char sec[2];

 hour[0]=strtimeHHMMSS[0];
 hour[1]=strtimeHHMMSS[1];
 hour[2] ='\0';
 int hourint = atoi(hour);

 if (hourint == 0 || hourint > 24 || hourint < 10) {
  return -1;
 }

 min[0]=strtimeHHMMSS[2];
 min[1]=strtimeHHMMSS[3];
 min[2] ='\0';
 int minint = atoi(min);

 sec[0]=strtimeHHMMSS[4];
 sec[1]=strtimeHHMMSS[5];
 sec[2] ='\0';
 int secint = atoi(sec);

 tm newTime1 = tm();
 newTime1.tm_sec=secint;
 newTime1.tm_min=minint;
 newTime1.tm_hour=hourint;
 newTime1.tm_mday=currentTimeNow->tm_mday;
 newTime1.tm_mon= currentTimeNow->tm_mon;
 newTime1.tm_year=currentTimeNow->tm_year;

 time_t mynewTimeT1 = mktime(&newTime1);




 char strtime2HHMMSS[6];
 //sprintf(strtime2HHMMSS,"%d",time2HHMMSS);
 sprintf(strtime2HHMMSS,"%d",time2HHMMSS);

 char hour2[2];
 char min2[2];
 char sec2[2];

 hour2[0]=strtime2HHMMSS[0];
 hour2[1]=strtime2HHMMSS[1];
 hour2[2] ='\0';
 int hourint2 = atoi(hour2);

 min2[0]=strtime2HHMMSS[2];
 min2[1]=strtime2HHMMSS[3];
 min2[2] ='\0';
 int minint2 = atoi(min2);

 sec2[0]=strtime2HHMMSS[4];
 sec2[1]=strtime2HHMMSS[5];
 sec2[2] ='\0';
 int secint2 = atoi(sec2);

 tm newTime2 = tm();
 newTime2.tm_sec=secint2;
 newTime2.tm_min=minint2;
 newTime2.tm_hour=hourint2;
 newTime2.tm_mday=currentTimeNow->tm_mday;
 newTime2.tm_mon= currentTimeNow->tm_mon;
 newTime2.tm_year=currentTimeNow->tm_year;

 time_t mynewTimeT2 = mktime(&newTime2);
 return (int) difftime(mynewTimeT2,mynewTimeT1);

}
/*
//pass in 123356,34 (will add 34 seconds to 123356)
//return  123429
int addSecondsToTime(int timeHHMMSS,int seconds){

time_t now;
time(&now);
struct tm * currentTimeNow = localtime(&now);
mktime(currentTimeNow);

char strtimeHHMMSS[6];
sprintf(strtimeHHMMSS,"%d",timeHHMMSS);

char hour[2];
char min[2];
char sec[2];

hour[0]=strtimeHHMMSS[0];
hour[1]=strtimeHHMMSS[1];
hour[2] ='\0';
int hourint = atoi(hour);

if (hourint == 0 || hourint > 24 || hourint < 10) {
return -1;
}

min[0]=strtimeHHMMSS[2];
min[1]=strtimeHHMMSS[3];
min[2] ='\0';
int minint = atoi(min);
if (minint <0  || minint > 60) {
return -1;
}

sec[0]=strtimeHHMMSS[4];
sec[1]=strtimeHHMMSS[5];
sec[2] ='\0';
int secint = atoi(sec);
if (secint <0  || secint > 60) {
return -1;
}


tm newTime = tm();
newTime.tm_sec=secint;
newTime.tm_min=minint;
newTime.tm_hour=hourint;
newTime.tm_mday=currentTimeNow->tm_mday;
newTime.tm_mon= currentTimeNow->tm_mon;
newTime.tm_year=currentTimeNow->tm_year;

time_t myTimeT = mktime(&newTime);
newTime.tm_sec = newTime.tm_sec + seconds;
mktime(&newTime);

int timePlusSeconds =newTime.tm_hour*10000 +  newTime.tm_min*100 + newTime.tm_sec;

return timePlusSeconds;
}
*/
//
// This will get the direction based off off all points in the structure
//
//

void getPriceDirection(char *p) {
 double *pCurve=NULL;
 int size= ((DATAPOINTS2*)p)->index;
 pCurve= ((DATAPOINTS2*)p)->priceCurve;

 int differencePtm1_Pt=0;
 int differencePt_Ptm1=0;
 double lnPricePoints_Pt_Ptm1=0.0;

 for (int x =0; x < size; x++ ) {
  differencePtm1_Pt += (int)( pCurve[x] - pCurve[x+1]);
  differencePt_Ptm1 += (int)( pCurve[x+1] - pCurve[x]);
  //lnPricePoints_Pt_Ptm1 += log (abs((int) (pCurve[x+1] - pCurve[x])));
  lnPricePoints_Pt_Ptm1 += log (fabs((pCurve[x+1] - pCurve[x])));
 }
 printf("-----> differencePt_Ptm1 %d  differencePtm1_Pt %d  lnPricePoints_Pt_Ptm1 %f \n\n ",
  differencePt_Ptm1,differencePtm1_Pt, lnPricePoints_Pt_Ptm1);
}






/*
double getRounded (double quantity) {
double remainder = (int) quantity / 100 ;

if (remainder >= 50) {
double foo = quantity-remainder+100;
printf("        remainder %f returning %f\n",remainder,foo);
}
else {
double foo = quantity - remainder;                  
printf("        remainder %f returning %f\n",remainder,foo);
}
return (remainder >= 50) ? quantity  - remainder + 100 : quantity-remainder
}
*/

//used to test if doubles are equal
int EQ_double ( double p1 , double p2 )
{
 if (fabs(p1 - p2) <= 0.0001)  {
  printf ("test.c: EQ_double: p1 %f - p2 %f = %f < 0.0001 \n",p1,p2,fabs(p1 - p2));
  return 1;
 }
 else {
  printf ("test.c: EQ_double: p1 %f - p2 %f = %f > 0.0001 \n",p1,p2,fabs(p1 - p2));
  return 0;
 }
}

//used to test if doubles are equal
int GT_double ( double p1 , double p2 )
{
 if (p1>p2)  return 1;
 else        return 0;
}
//used to test if doubles are equal
int LT_double ( double p1 , double p2 )
{
 if (p1 < p2)  return 1;
 else          return 0;
}

//returns a large

int  getRounded1 (int quantity) {
 //return (quantity % 100 == 0);
 int remain= quantity % 100;
 return remain;
}

//Give 65.378765;
//return 65.37000 //non rounding, unlike sprintf
//
double chopPrecision(double p1) {

 double fractpart,intpart,intpart2;
 double one=1;
 fractpart=modf(p1,&intpart);
 fractpart=modf(fractpart*100,&intpart2);

 if(fractpart > one  ||
  EQ_double(fractpart,1)) {
   printf ("   >=1     routine:p1 %f fractpart %f\n",p1,fractpart);
   return p1;
 }
 else {
  printf ("   <1      routine:p1 %f fractpart %f --subtract fabs(one - fractpart) %f \n",p1,
   fractpart,
   fabs(one-fractpart));
  return (intpart + intpart2/100);
 }

}


int main() {
 static const char* SEP=",";
 printf("%s Seperator",SEP);

 /**** TEST *********/
 double currentVolume = 12000;
 double volumeAtStartOfOrder = 10000;
 double volPct = 33;

 double qtyToExecute =  (currentVolume - volumeAtStartOfOrder) * (volPct/100);

 if (qtyToExecute <= 0) {
  printf ("KNICKS!!!!\n");
 }
 printf("\n %f qtyToExecute\n",qtyToExecute);
 //
 //double val = getRounded(502);
 //              printf(" %f getRounded \n",val);

 double value= 602;  
 int val1 = getRounded1((int) value);
 printf(" %d getRounded \n",val1);

 /*
 * test
 char msg[2];
 int t=0;
 while ( t<40){

 sprintf(msg,"%s %s\n","foodoo");

 printf ("t:%d  msg %s sizeof %d\n ---\n",t,msg,sizeof(msg));

 int myint=400000;
 printf ("test myint %f \n",myint);
 t++;
 }
 */

 /*TEST2*/

 char charMon[2];
 int  month=10;
 sprintf(charMon,"%02d",month);
 printf( "test2: the charMon %s \n",charMon);

 /* TEST 3*/

 char cvar='A';

 (cvar == 'T' || cvar =='B' ) ?  printf(" test 3: YES\n") : printf("test 3: no\n");

 /*TEST 4*/  
 char charMon1[1];
 char * month1="ONETWOTHREEFOUR";
 sprintf(charMon1,"I am testing %s",month1);
 printf( "test 4: 1: the charMon %s sizeof(charMon1) %d strlen %d \n",charMon1,sizeof(charMon1),strlen(charMon1));

 month1="SIZESEVE";
 sprintf(charMon1,"%s",month1);
 printf( "test 4: 2: the charMon %s sizeof(charMon1) %d strlen %d \n",charMon1,sizeof(charMon1),strlen(charMon1));


 /*TEST 5*/  
 char buffer [10];
 int n, a=5, b=3;
 n=sprintf (buffer, "%d plus %d is %d", a, b, a+b);
 printf ("test 5 [%s] is a %d chars string:strlen %d\n",buffer,n,strlen(buffer));

 /*TEST 6*/
 double mydbl=0;
 int myint =23;
 mydbl = (double) myint;
 printf ("test 6 mydl %f\n",mydbl);

 /*TEST7*/
 double mydbl1=0;
 char * mycptr1 ="234";
 mydbl1 = atoi(mycptr1);
 printf ("test 7 mydl1 %f\n",mydbl1);

 /*Test 8 */



 double wait_time = 10 + (rand()/(double)RAND_MAX)*2;

 printf("test8: random time 10+rand()/double RAND_MAX= %f ,rand() %f\n",wait_time,(rand()/(double)RAND_MAX)*2);

 /*Test 9 */

 double one =7.213478;
 double two  =7.217567;

 char foo[4];
 sprintf(foo,"%.2f",one);

 double onechanged=atof(foo);

 sprintf(foo,"%.2f",two);

 double twochanged=atof(foo);

 if ( one < two ) {
  printf("test 9 Expected\n");
 }
 else if (one == two) {
  printf("test9 Equal\n");
 }

 if (onechanged <twochanged) {
  printf("test9 DEAD ON, onechanged %f, twochanged %f \n",onechanged,twochanged);
 }
 else if (onechanged == twochanged ) {
  printf("test9 Equal , onechanged %f, twochanged %f \n",onechanged,twochanged);
 }

 /*test 10 */
 double p1 =2.269876;
 double p2 =2.270001;

 printf ( "test10 fabs (p1 - p2) %f\n",fabs(p1 -p2));

 if (fabs(p1 - p2) <= 0.01){

  printf ( "test10 they are equal \n");
 }
 else {
  printf ( "test10 not equal \n");
 }

 double p3=65.51;
 double p4=65.519345;

 double fractpart,intpart,intpart2;

 printf ( "test10  ---  p4 %.3f \n",p4);
 printf ( "test10  ---  floor(p4) %.3f \n",floor(p4));

 fractpart=modf(p4,&intpart);
 printf("1. fractpart %f \n ", fractpart);
 double temp=fractpart*100;
 printf("temp  %f \n ", temp);

 fractpart=modf(temp,&intpart2);
 printf("2. fractpart %f intpart2 %f \n ", fractpart,intpart2);
 double toadd=intpart2/100;
 printf("toadd %f \n ", toadd);

 printf ( "test10  was p4 %f now is intpart %f \n",p4,intpart+toadd);
 printf ( "test10  was p4 %f now is chopPrecision(p4) %f \n",p4,chopPrecision(p4));


 if (EQ_double(p3,p4)) {
  printf ( "test10  --- equal p3 %f p4 %f \n",p3,p4);
 }

 if (GT_double(p3,p4)) {
  printf ( "test10  --- p3 %f > p4 %f \n",p3,p4);
 }

 if (LT_double(p3,p4)) {
  printf ( "test10  --- p3 %f < p4 %f \n",p3,p4);
 }

 /* Test 11 */

 double p5=65.54;
 printf ( "\n\n test11  was p5 %f now is chopPrecision(p5) %f \n",p5,chopPrecision(p5));

 double e1,e2,e3,e4,e5,e6,e7=0;

 /* Test 12 */
 char str[7];
 sprintf(str,"%s","Test");

 if (strstr(str,"est")) {
  printf("test 12 got a match\n");
 }
 else {
  printf("test12 no match \n");
 }


 /* test 13 */
 printf ("test13 Pctage   %f\n",((39.05-39.01)/39.05 ) * 100 );

 /* test 14 */
 double foo23= 0.021234;
 char cstr14[5];
 sprintf (cstr14,"%.2f\n",fabs(foo23));
 double fooresult=atof(cstr14);

 printf ("test14 2. deviation   %f\n",fooresult);

 /*test 15 */
 int num=0;
 2 > 4 ? num=1 :num =2;
 printf ("test 15:num is %d \n",num);

 /*test 16 */

 int dlbarr[]= {1,2,3,4,5};

 printf("test16 size is %d\n", sizeof(dlbarr));

 /*** test 17 ***********/

 ptrDataPoint2 = (DATAPOINTS2*) malloc(sizeof(*ptrDataPoint2));
 //simulate Get_UserPointer
 char *cptr = reinterpret_cast<char *>(ptrDataPoint2);

 ptrDataPoint2->priceCurve[0]=25;
 ptrDataPoint2->priceCurve[1]=20;
 ptrDataPoint2->priceCurve[2]=18;
 ptrDataPoint2->priceCurve[3]=17;
 ptrDataPoint2->priceCurve[4]=20;
 ptrDataPoint2->priceCurve[5]=21;
 ptrDataPoint2->priceCurve[6]=17;
 ptrDataPoint2->priceCurve[4]=16;
 ptrDataPoint2->priceCurve[7]=14;
 ptrDataPoint2->index=7;

 printf("test17:mixed but major down trend direction:getPriceDirection() -->\n");
 getPriceDirection(cptr);



 ptrDataPoint2->priceCurve[0]=25;
 ptrDataPoint2->priceCurve[1]=20;
 ptrDataPoint2->priceCurve[2]=18;
 ptrDataPoint2->priceCurve[3]=17;
 ptrDataPoint2->priceCurve[4]=16;
 ptrDataPoint2->priceCurve[5]=15;
 ptrDataPoint2->priceCurve[6]=14;
 ptrDataPoint2->priceCurve[4]=13;
 ptrDataPoint2->priceCurve[7]=12;

 printf("test17 :Pure down trend direction:getPriceDirection() --> \n");
 getPriceDirection(cptr);


 ptrDataPoint2->priceCurve[0]=14;
 ptrDataPoint2->priceCurve[1]=16;
 ptrDataPoint2->priceCurve[2]=17;
 ptrDataPoint2->priceCurve[3]=18;
 ptrDataPoint2->priceCurve[4]=20;
 ptrDataPoint2->priceCurve[5]=21;
 ptrDataPoint2->priceCurve[6]=24;
 ptrDataPoint2->priceCurve[4]=28;
 ptrDataPoint2->priceCurve[7]=29;


 printf("test17 : Pure upTrend direction:getPriceDirection() --> \n");
 getPriceDirection(cptr);


 ptrDataPoint2->priceCurve[0]=14;
 ptrDataPoint2->priceCurve[1]=16;
 ptrDataPoint2->priceCurve[2]=17;
 ptrDataPoint2->priceCurve[3]=18;
 ptrDataPoint2->priceCurve[4]=10;
 ptrDataPoint2->priceCurve[5]=14;
 ptrDataPoint2->priceCurve[6]=17;
 ptrDataPoint2->priceCurve[4]=21;
 ptrDataPoint2->priceCurve[7]=22;


 printf("test17 : mixed but major upTrend direction: getPriceDirection() --> \n");
 getPriceDirection(cptr);


 ptrDataPoint2->priceCurve[0]=61.76;
 ptrDataPoint2->priceCurve[1]=61.76;
 ptrDataPoint2->priceCurve[2]=61.85;
 ptrDataPoint2->priceCurve[3]=61.84;
 ptrDataPoint2->priceCurve[4]=61.75;
 ptrDataPoint2->priceCurve[5]=61.80;
 ptrDataPoint2->priceCurve[6]=61.79;
 ptrDataPoint2->priceCurve[4]=61.76;
 ptrDataPoint2->priceCurve[7]=61.75;

 printf("test17 : mixed -->getPriceDirection() \n");
 getPriceDirection(cptr);

 free(cptr);

 /*** test 18****/

 printf("test18 :%d \n",123423 % 1000000);
 printf("test18 :%d \n",123423 % 100000);
 printf("test18 :%d \n",123423 % 10000);
 printf("test18 :%d \n",123423 % 10000);

 /*** test 19****/

 char stet[]="152345-152623";
 char * ptr_stet;

 printf("test19 strlen stet %d \n",strlen(stet));
 ptr_stet = strtok(stet,"-");
 while(ptr_stet!=NULL) {
  printf("test19 time:%f \n",atof(ptr_stet));
  ptr_stet = strtok(NULL,"-");

 }

 /**Test 20 ****/
 time_t now;
 time(&now);
 struct tm * timeNow = localtime(&now);
 mktime(timeNow);


 int startTime = 120000;
 //int startTime = 155800;
 int endTime = 121000;
 int numSlices= 5;
 int unloadTime = 500;

 int currentTime =timeNow->tm_hour*10000+timeNow->tm_min*100+timeNow->tm_sec;
 timeNow->tm_sec += 900;

 mktime(timeNow);

 int currentTimePlus15Min =timeNow->tm_hour*10000+timeNow->tm_min*100+timeNow->tm_sec;
 int currentTimeAsInt = currentTime/10000*3600 + currentTime/100*60+ currentTime%100;
 int start = startTime/10000*3600 + startTime/100*60+ startTime%100;
 int end = endTime/10000*3600 + endTime/100*60 + endTime%100;
 int temp1 = endTime/10000*3600 + endTime/100%100*60 + endTime%100- unloadTime;
 int temp2 =temp1/3600*10000+(temp1/60)%60*100+temp1%60;

 int nextTimeToSendSlice = 121020+300;
 int nextTimeToSendSlice2 = nextTimeToSendSlice/10000*3600 + nextTimeToSendSlice/100*60+nextTimeToSendSlice%100;


 if (nextTimeToSendSlice%100>=60){
  nextTimeToSendSlice+=100;
  nextTimeToSendSlice-=60;
  printf("1. test20: currentTime %d currentTimePlus15Min %d currentTimeAsInt %d start %d end %d end-start %d SlicesTimeApart %d temp1 %d temp2 %d nextTimetoSendSlice%d\n\n", currentTime,currentTimePlus15Min,currentTimeAsInt,start,end,(end-start),(end-start)/numSlices,temp1,temp2,nextTimeToSendSlice);

 }

 if (nextTimeToSendSlice%10000>=6000){

  nextTimeToSendSlice+=10000;
  nextTimeToSendSlice-=6000;
  printf("2. test20: currentTime %d currentTimePlus15Min %d currentTimeAsInt %d start %d end %d end-start %d SlicesTimeApart %d temp1 %d temp2%d nextTimeToSendSlice %d\n\n", currentTime,currentTimePlus15Min,currentTimeAsInt,start,end,(end-start),(end-start)/numSlices,temp1,temp2,nextTimeToSendSlice);

 }

 printf("3. test20: currentTime %d currentTimePlus15Min %d currentTimeAsInt %d start %d end %d end-start %d SlicesTimeApart %d temp1 %d temp2%d nextTimeToSendSlice %d nextTimeToSendSlice2 %d \n\n", currentTime,currentTimePlus15Min,currentTimeAsInt,start,end,(end-start),(end-start)/numSlices,temp1,temp2,nextTimeToSendSlice,nextTimeToSendSlice2);

 /*************** test 21 ****************************/
 tm tmpTime= tm();

 tmpTime.tm_sec=57;
 tmpTime.tm_min=41;
 tmpTime.tm_hour=14;
 tmpTime.tm_mday=2;
 tmpTime.tm_mon= 1;
 tmpTime.tm_year=2006-1900;

 time_t myTimeT = mktime(&tmpTime);


 tm tmpTime1= tm();
 tmpTime1.tm_sec=02;
 tmpTime1.tm_min=42;
 tmpTime1.tm_hour=14;
 tmpTime1.tm_mday=2;
 tmpTime1.tm_mon= 1;
 tmpTime1.tm_year=2006-1900;

 time_t myTimeT2 = mktime(&tmpTime1);

 printf(" test21: the diff %f \n\n\n",difftime(myTimeT2,myTimeT));

 /********** test 22 ***********/

 int st1=150000;
 //int subtractsecs= 30;
 int subtractsecs= 30;

 int st2=154500;
 int st3=154607;

 //int st4=154558;
 //int addsecs =35;

 int st4=150000;
 //int addsecs =35;
 int addsecs =300;

 printf("test22:1 :st1 %d  subtractSeconds %d subtractSecondsfromTime %d \n",st1,subtractsecs,subtractSecondsFromTime(st1,subtractsecs));

 /****************** test 24 *************/

 time_t now123;
 time(&now123);

 struct tm * timeNow123 = localtime(&now123);
 mktime(timeNow123);

 int currentTime123 =timeNow123->tm_hour*10000+timeNow123->tm_min*100+timeNow123->tm_sec;
 int current123 = currentTime123/10000*3600 + currentTime123/100*60+currentTime123%100;

 printf("test 24 timeNow123->tm_hour %d,timeNow123->tm_hour*10000 %d,timeNow123->tm_min %d,timeNow123->tm_min*100 %d,timeNow123->tm_sec %d \n",

  timeNow123->tm_hour,timeNow123->tm_hour*10000,timeNow123->tm_min,timeNow123->tm_min*100,timeNow123->tm_sec);

 printf("test 24 currentTime123 %d \n",currentTime123);
 /*
 int startTime=
 int endTime =
 int end = endTime/10000*3600 + endTime/100*60+endTime%100;
 int start = startTime/10000*3600 + startTime/100*60+startTime%100;
 */

 /*********************test 25 ***************/
 /*
 char stet[]="152345-152623";
 char * ptr_stet;

 printf("test19 strlen stet %d \n",strlen(stet));
 ptr_stet = strtok(stet,"-");
 while(ptr_stet!=NULL) {
 printf("test19 time:%f \n",atof(ptr_stet));
 ptr_stet = strtok(NULL,"-");

 }

 */

 char  strfoo[] ="34.58-10000-34.89-250000-36.78-24000";
 char * ptr_strfoo=NULL;

 ptr_strfoo = strtok(strfoo,"-");


 //printf("test25  1:%f \n",atof(ptr_strfoo));
 //printf("test 25 strlen ptr_strfoo %d\n",strlen(ptr_strfoo));

 while(ptr_strfoo != NULL) {
  /*
  char pxvol[strlen(ptr_strfoo)];
  strcpy(pxvol,ptr_strfoo);
  char * ptr_pxvol=NULL;
  ptr_pxvol= strtok(pxvol,":");

  while(ptr_pxvol != NULL) {
  printf("test25 -----b Value-->%f \n",atof(ptr_pxvol));
  ptr_pxvol= strtok(NULL,":");
  }
  */
  /* Below work
  printf("test25  Price:%s \n",ptr_strfoo);
  ptr_strfoo = strtok(NULL,"-");
  printf("test25  Qty :%s \n",ptr_strfoo);
  ptr_strfoo = strtok(NULL,"-");
  */

  printf("test25  Price:%f \n",atof(ptr_strfoo));
  ptr_strfoo = strtok(NULL,"-");
  printf("test25  Qty :%f \n",atof(ptr_strfoo));
  ptr_strfoo = strtok(NULL,"-");
 }  


 /*********** test 26 **********/
 char * fooarr[5];
 fooarr[0]= (char*)malloc(100);
 strcpy(fooarr[0],"1 This is test26.Array of char ptrs");

 fooarr[1]= (char*)malloc(100);
 strcpy(fooarr[1],"2 test 26 .Box car racers ar here");

 fooarr[2]= (char*)malloc(100);
 strcpy(fooarr[2],"3 test 26 .Box car racers ar here");

 fooarr[3]= (char*)malloc(100);
 strcpy(fooarr[3],"4. test 26 .Box car racers ar here");

 fooarr[4]= (char*)malloc(100);
 strcpy(fooarr[4],"5. test 26 .Box car racers ar here");

 fooarr[5]= (char*)malloc(100);
 strcpy(fooarr[5],"6. test 26 .Box car racers ar here");
/* Need to malloc more space or else below will run out of space
 for (int x =0; x< 5;x++) {

  printf("test26  fooarr x %d %s\n ",x,fooarr[x]);
 }
 */

 free(*fooarr);

 /*********** test 27 **********/

 /*** test 27****/

 char symstr[]="ABC.O";
 char * place;

 place = strtok(symstr,".");

 printf("test27 1 time:%s \n",place);
 place = strtok(NULL,".");
 printf("test27 2 time:%s \n",place);

 /*********** test 28 **********/


 double price1 = 28.75;
 double price2 = 28.7589;    
 double price3 = 28.7524;    
 double price4 = 28.7;
 printf("test28 price %f  roundPrice %f\n",price1,roundPrice("buy",price1));
 printf("test28 price %f  roundPrice %f\n",price2,roundPrice("buy",price2));
 printf("test28 price %f  roundPrice %f\n",price3,roundPrice("buy",price3));
 printf("test28 price %f  roundPrice %f\n",price4,roundPrice("buy",price4));

 /*********** test 29 **********/

 string sfoo = "5000@20=100000";
 getSmartExpStr(sfoo);

 /*********** test 30 **********/

 //              string sf=getExpStr(500,20.2);


 //              printf("getExpStr %s\n",sf.c_str());
 string sf= ToString<int>(42);
 printf("test 30 sf %s\n",sf.c_str());

 string p1977=getExpStr(1000000,180.98);
 printf("test 30 getExpStr %s\n",p1977.c_str());

 p1977=getExpStr(500,20.2);
 printf("test 30 getExpStr %s\n",p1977.c_str());

 /*********** test 31 **********/

 printf ("test 31 percentd %d \n",25/2);
 printf ("test 31 percentf %f \n",25/2);
 printf ("test 31 percentfABS %f \n",abs(25/2));

 /*********** test 32 **********/

 double * ptr_double = testPtrReturn();
 printf("test 32 ptr_double :testPtrReturn() %f\n", *ptr_double);

 double  ptr_double2 = *testPtrReturn();
 printf("test 32 ptr_double2 :testPtrReturn()  %f\n", ptr_double2);


 /*********** test 34 **********/

 //printf("testing 34 ref return  %s\n", testStrRefRtrn());

 /*********** test 35 **********/

 A aobj;
 A* aptr = new A();
 A& aref=(*aptr);
 delete (aptr);

 /*********** test 35 **********/
 //FOO foo foo

 class Ins {
 public:
  virtual void foo() {
   printf("Test 35 : class Ins: foo \n");
  }
  virtual ~Ins(){}

 };

 class fx : public Ins {
 public:
  virtual void foo() {
   printf("Test 35 : class fx: foo \n");
  }

  virtual ~fx(){}
 };

 Ins *iptr = new Ins();
 fx * fptr = new fx();
 iptr=&(*fptr);

 iptr->foo();
 delete (iptr); // fptr will be gone as wellss
 //delete (fptr);


 /***** test 37************/


 MZSingleton *myptrz = MZSingleton::getMZSingleton1();
 MZSingleton *myptrz2 = myptrz->getMZSingleton1();
 //                        MZSingleton & myrefz =MZSingleton::getMZSingleton1();
 // Singleton *p2 = p1->Instance();
 // Singleton & ref = * Singleton::Instance();

 return 0;
}
/*

#include <sys/time.h>
    typedef unsigned long long timestamp_t;

    static timestamp_t
    get_timestamp ()
    {
      struct timeval now;
      gettimeofday (&now, NULL);
      return  now.tv_usec + (timestamp_t)now.tv_sec * 1000000;
    }

    ...
    timestamp_t t0 = get_timestamp();
    // Process
    timestamp_t t1 = get_timestamp();

    double secs = (t1 - t0) / 1000000.0L;

 

*/ 

/*

uint64 GetTimeMs64()
{
#ifdef _WIN32
 /* Windows */
 FILETIME ft;
 LARGE_INTEGER li;

 /* Get the amount of 100 nano seconds intervals elapsed since January 1, 1601 (UTC) and copy it
  * to a LARGE_INTEGER structure. */
 GetSystemTimeAsFileTime(&ft);
 li.LowPart = ft.dwLowDateTime;
 li.HighPart = ft.dwHighDateTime;

 uint64 ret = li.QuadPart;
 ret -= 116444736000000000LL; /* Convert from file time to UNIX epoch time. */
 ret /= 10000; /* From 100 nano seconds (10^-7) to 1 millisecond (10^-3) intervals */

 return ret;
#else
 /* Linux */
 struct timeval tv;

 gettimeofday(&tv, NULL);

 uint64 ret = tv.tv_usec;
 /* Convert from micro seconds (10^-6) to milliseconds (10^-3) */
 ret /= 1000;

 /* Adds the seconds (10^0) after converting them to milliseconds (10^-3) */
 ret += (tv.tv_sec * 1000);

 return ret;
#endif
}

*/




/*
*what went bang
*/<pre>#include 
#include 
#include 
#include <math>
#include 
#include 
#include 
#include 
#include 
 
#define NUM_THREADS 20

using namespace std;

int subtractSecondsFromTime( int, int);
//int getSecondsBetweenTime ( int,int );
//int addSecondsToTime(int,int );
int subtractSecondsFromTime(int ,int );
int addSecondsToTime(int ,int );
int copychar (const char* , char* );

void getPriceDirection(char *p);
double getRounded(double);
int    getRounded1(int);

int EQ_double(double , double );
int GT_double(double , double );
int LT_double(double , double );
double chopPrecision (double);

void getSmartExpStr(string );

string getExpStr(int,double);

template
string ToString(const I&amp;);

/********/
const int POINTS= 25; //from 0 to 25 = 26 ( 9:30 = OPEN, 945,1000 .... 1545)
typedef struct {
 double priceCurve[POINTS];
 int index;
 int first;
} DATAPOINTS2;

DATAPOINTS2* ptrDataPoint2;
 
typedef struct {
 int qty;
 double price;
 double notional;
} SMART_EXP_STR;

SMART_EXP_STR* smartExpStr;

double roundPrice(string , double );
double *  testPtrReturn();
string&amp; testStrRefRtrn();

/********/
class A {
public:
 A() {
  printf ("In Ctor of A \n");
 }

};
/********/
class MZSingleton {

public:
 static MZSingleton* getMZSingleton1();
 static MZSingleton* getMZSingleton2();
protected:
 MZSingleton();
 MZSingleton(const MZSingleton&amp;);
 MZSingleton&amp; operator=(const MZSingleton&amp;);
private:
 static MZSingleton* MZSingleton_ptr;

};


MZSingleton* MZSingleton::MZSingleton_ptr=0;// initialize pointer

MZSingleton* MZSingleton::getMZSingleton1()
{
 if (MZSingleton_ptr == 0)  // is it the first call?
 {
  MZSingleton_ptr = new MZSingleton; // create sole instance
 }
 return MZSingleton_ptr; // address of sole instance
}
MZSingleton* MZSingleton::getMZSingleton2()
{
 static MZSingleton MZSingleton_ref;
 return &amp;MZSingleton_ref;
}
MZSingleton::MZSingleton()
{

}


/********/
/*
string&amp; testStrRefRtrn() {
string  &amp;strt123;
strt123 = new string("foo");
return strt123;
}
*/
double* testPtrReturn() {
 double fo = 23.45;

 double* p=&amp;fo;


 return p;

 //      double w      = 880.24;
 //      double *ws    = &amp;w;
 //      double **wwss = &amp;ws;

 //      return wwss;

}

string getExpStr(int qty, double price ) {

 double notional = qty*price;

 char str[50];

 //sprintf_s(str,"%-20.4f",notional);
 sprintf(str,"%-20.4f",notional);
 

 string retstr;
 retstr =  ToString<int>(qty);
 retstr += "@$";
 retstr += ToString(price);
 retstr += "=$";
 //retstr += ToString(notional);
 retstr += str;

 return retstr;

}
template
std::string ToString(const T&amp; val)
{
 std::stringstream strm;
 strm &lt;&lt; val;
 return strm.str();
}

void setSmartExpStr(SMART_EXP_STR* sPtr,int qty,double px,double notional) {

 sPtr = (SMART_EXP_STR*) malloc(sizeof(*sPtr));

 sPtr-&gt;qty=qty;
 sPtr-&gt;price=px;
 sPtr-&gt;notional=notional;

}

//SMART_EXP_STR* getSmartExpStr(string smartExpStr) {

void getSmartExpStr(string smartExpStr) {
 //char stet[]="152345-152623";
 //
 //
 //

 //int len = strlen(smartExpStr.c_str());
 char *stet= const_cast (smartExpStr.c_str());
 char * ptr_stet;

 printf("test29 strlen stet %d \n",strlen(stet));
 ptr_stet = strtok(stet,"@");
 printf("test29 Qty:%d \n",atoi(ptr_stet));
 ptr_stet = strtok(NULL,"@");
 printf("test29 px:%f \n",atoi(ptr_stet));

}

double roundPrice(string side, double price){
 double returnPrice = -1;
 if (side=="buy"){
  double tmp1 = 100*price;
  int tmp2 = (int)tmp1;
  double buying = (double)(tmp2)/100;
  returnPrice = buying;
 }else{
  double tmp3 = price + 0.009;
  double tmp4 = 100*tmp3;
  int tmp5 = (int)tmp4;
  double selling = ((double)tmp5)/100;
  returnPrice = selling;
 }
 return returnPrice;
}

int subtractSecondsFromTime(int time1,int secs){
 int temp = time1/10000*3600  + (time1%10000)/100*60 + time1%100;
 temp-=secs;
 return temp/3600*10000  +   temp%3600/60*100 +  temp%60;
}

int addSecondsToTime(int time1,int secs){
 int temp = time1/10000*3600+  (time1%10000)/100*60 + time1%100;
 temp+=secs;
 return temp/3600*10000+temp%3600/60*100+temp%60;
}

/*
// pass in 123425,29 (will subtract 29 seconds from 123425)
// return 123356
int subtractSecondsFromTime( int timeHHMMSS,int seconds) {
time_t now;
time(&amp;now);
struct tm * currentTimeNow = localtime(&amp;now);
mktime(currentTimeNow);

char strtimeHHMMSS[6];
sprintf(strtimeHHMMSS,"%d",timeHHMMSS);

char hour[2];
char min[2];
char sec[2];

hour[0]=strtimeHHMMSS[0];
hour[1]=strtimeHHMMSS[1];
hour[2] ='\0';
int hourint = atoi(hour);

if (hourint == 0 || hourint &gt; 23 || hourint &lt;10 -1="" if="" int="" min="" minint="" return="" strtimehhmmss=""&gt; 59) {
return -1;
}

sec[0]=strtimeHHMMSS[4];
sec[1]=strtimeHHMMSS[5];
sec[2] ='\0';
int secint = atoi(sec);
if (secint &lt;0 secint=""&gt; 59) {
return -1;
}


tm newTime = tm();
newTime.tm_sec=secint;
newTime.tm_min=minint;
newTime.tm_hour=hourint;
newTime.tm_mday=currentTimeNow-&gt;tm_mday;
newTime.tm_mon= currentTimeNow-&gt;tm_mon;
newTime.tm_year=currentTimeNow-&gt;tm_year;

time_t myTimeT = mktime(&amp;newTime);
newTime.tm_sec = newTime.tm_sec - seconds;
newTime.tm_min = newTime.tm_min;
mktime(&amp;newTime);

printf ("tm_min tim_min %d tim_min*100 %d \n",newTime.tm_min,newTime.tm_min*100);
int timeMinusSeconds =newTime.tm_hour*10000 +  newTime.tm_min*100 + newTime.tm_sec;
}
*/

// pass in 123356,123429
// return 34
int getSecondsBetweenTime ( int time1HHMMSS,int time2HHMMSS) {
 time_t now;
 time(&amp;now);
 struct tm * currentTimeNow = localtime(&amp;now);
 mktime(currentTimeNow);

 char strtimeHHMMSS[6];
 sprintf(strtimeHHMMSS,"%d",time1HHMMSS);

 char hour[2];
 char min[2];
 char sec[2];

 hour[0]=strtimeHHMMSS[0];
 hour[1]=strtimeHHMMSS[1];
 hour[2] ='\0';
 int hourint = atoi(hour);

 if (hourint == 0 || hourint &gt; 24 || hourint &lt; 10) {
  return -1;
 }

 min[0]=strtimeHHMMSS[2];
 min[1]=strtimeHHMMSS[3];
 min[2] ='\0';
 int minint = atoi(min);

 sec[0]=strtimeHHMMSS[4];
 sec[1]=strtimeHHMMSS[5];
 sec[2] ='\0';
 int secint = atoi(sec);

 tm newTime1 = tm();
 newTime1.tm_sec=secint;
 newTime1.tm_min=minint;
 newTime1.tm_hour=hourint;
 newTime1.tm_mday=currentTimeNow-&gt;tm_mday;
 newTime1.tm_mon= currentTimeNow-&gt;tm_mon;
 newTime1.tm_year=currentTimeNow-&gt;tm_year;

 time_t mynewTimeT1 = mktime(&amp;newTime1);




 char strtime2HHMMSS[6];
 //sprintf(strtime2HHMMSS,"%d",time2HHMMSS);
 sprintf(strtime2HHMMSS,"%d",time2HHMMSS);

 char hour2[2];
 char min2[2];
 char sec2[2];

 hour2[0]=strtime2HHMMSS[0];
 hour2[1]=strtime2HHMMSS[1];
 hour2[2] ='\0';
 int hourint2 = atoi(hour2);

 min2[0]=strtime2HHMMSS[2];
 min2[1]=strtime2HHMMSS[3];
 min2[2] ='\0';
 int minint2 = atoi(min2);

 sec2[0]=strtime2HHMMSS[4];
 sec2[1]=strtime2HHMMSS[5];
 sec2[2] ='\0';
 int secint2 = atoi(sec2);

 tm newTime2 = tm();
 newTime2.tm_sec=secint2;
 newTime2.tm_min=minint2;
 newTime2.tm_hour=hourint2;
 newTime2.tm_mday=currentTimeNow-&gt;tm_mday;
 newTime2.tm_mon= currentTimeNow-&gt;tm_mon;
 newTime2.tm_year=currentTimeNow-&gt;tm_year;

 time_t mynewTimeT2 = mktime(&amp;newTime2);
 return (int) difftime(mynewTimeT2,mynewTimeT1);

}
/*
//pass in 123356,34 (will add 34 seconds to 123356)
//return  123429
int addSecondsToTime(int timeHHMMSS,int seconds){

time_t now;
time(&amp;now);
struct tm * currentTimeNow = localtime(&amp;now);
mktime(currentTimeNow);

char strtimeHHMMSS[6];
sprintf(strtimeHHMMSS,"%d",timeHHMMSS);

char hour[2];
char min[2];
char sec[2];

hour[0]=strtimeHHMMSS[0];
hour[1]=strtimeHHMMSS[1];
hour[2] ='\0';
int hourint = atoi(hour);

if (hourint == 0 || hourint &gt; 24 || hourint &lt; 10) {
return -1;
}

min[0]=strtimeHHMMSS[2];
min[1]=strtimeHHMMSS[3];
min[2] ='\0';
int minint = atoi(min);
if (minint &lt;0 minint=""&gt; 60) {
return -1;
}

sec[0]=strtimeHHMMSS[4];
sec[1]=strtimeHHMMSS[5];
sec[2] ='\0';
int secint = atoi(sec);
if (secint &lt;0 secint=""&gt; 60) {
return -1;
}


tm newTime = tm();
newTime.tm_sec=secint;
newTime.tm_min=minint;
newTime.tm_hour=hourint;
newTime.tm_mday=currentTimeNow-&gt;tm_mday;
newTime.tm_mon= currentTimeNow-&gt;tm_mon;
newTime.tm_year=currentTimeNow-&gt;tm_year;

time_t myTimeT = mktime(&amp;newTime);
newTime.tm_sec = newTime.tm_sec + seconds;
mktime(&amp;newTime);

int timePlusSeconds =newTime.tm_hour*10000 +  newTime.tm_min*100 + newTime.tm_sec;

return timePlusSeconds;
}
*/
//
// This will get the direction based off off all points in the structure
//
//

void getPriceDirection(char *p) {
 double *pCurve=NULL;
 int size= ((DATAPOINTS2*)p)-&gt;index;
 pCurve= ((DATAPOINTS2*)p)-&gt;priceCurve;

 int differencePtm1_Pt=0;
 int differencePt_Ptm1=0;
 double lnPricePoints_Pt_Ptm1=0.0;

 for (int x =0; x &lt; size; x++ ) {
  differencePtm1_Pt += (int)( pCurve[x] - pCurve[x+1]);
  differencePt_Ptm1 += (int)( pCurve[x+1] - pCurve[x]);
  //lnPricePoints_Pt_Ptm1 += log (abs((int) (pCurve[x+1] - pCurve[x])));
  lnPricePoints_Pt_Ptm1 += log (fabs((pCurve[x+1] - pCurve[x])));
 }
 printf("-----&gt; differencePt_Ptm1 %d  differencePtm1_Pt %d  lnPricePoints_Pt_Ptm1 %f \n\n ",
  differencePt_Ptm1,differencePtm1_Pt, lnPricePoints_Pt_Ptm1);
}






/*
double getRounded (double quantity) {
double remainder = (int) quantity / 100 ;

if (remainder &gt;= 50) {
double foo = quantity-remainder+100;
printf("        remainder %f returning %f\n",remainder,foo);
}
else {
double foo = quantity - remainder;                    
printf("        remainder %f returning %f\n",remainder,foo);
}
return (remainder &gt;= 50) ? quantity  - remainder + 100 : quantity-remainder
}
*/

//used to test if doubles are equal
int EQ_double ( double p1 , double p2 )
{
 if (fabs(p1 - p2) &lt;= 0.0001)  {
  printf ("test.c: EQ_double: p1 %f - p2 %f = %f &lt; 0.0001 \n",p1,p2,fabs(p1 - p2));
  return 1;
 }
 else {
  printf ("test.c: EQ_double: p1 %f - p2 %f = %f &gt; 0.0001 \n",p1,p2,fabs(p1 - p2));
  return 0;
 }
}

//used to test if doubles are equal
int GT_double ( double p1 , double p2 )
{
 if (p1&gt;p2)  return 1;
 else        return 0;
}
//used to test if doubles are equal
int LT_double ( double p1 , double p2 )
{
 if (p1 &lt; p2)  return 1;
 else          return 0;
}

//returns a large

int  getRounded1 (int quantity) {
 //return (quantity % 100 == 0);
 int remain= quantity % 100;
 return remain;
}

//Give 65.378765;
//return 65.37000 //non rounding, unlike sprintf
//
double chopPrecision(double p1) {

 double fractpart,intpart,intpart2;
 double one=1;
 fractpart=modf(p1,&amp;intpart);
 fractpart=modf(fractpart*100,&amp;intpart2);

 if(fractpart &gt; one  ||
  EQ_double(fractpart,1)) {
   printf ("   &gt;=1     routine:p1 %f fractpart %f\n",p1,fractpart);
   return p1;
 }
 else {
  printf ("   &lt;1 --subtract="" -="" 38:="" ::::hello="" _thread_data_t="" arg="" argument="" create="" ctr="" d="" data-="" data="(thread_data_t" double="" f="" fabs="" for="" fractpart="" from="" function="" i="" id:="" int="" intpart2="" intpart="" n="" one-fractpart="" one="" p1="" printf="" return="" routine:p1="" struct="" stuff="" test38::::="" test="" thr_func="" thread="" thread_data_t="" tid="" typedef="" void=""&gt;tid,i);
 }
 
  pthread_exit(NULL);
}
 
int main() {
 static const char* SEP=",";
 printf("%s Seperator",SEP);

 /**** TEST *********/
 double currentVolume = 12000;
 double volumeAtStartOfOrder = 10000;
 double volPct = 33;

 double qtyToExecute =  (currentVolume - volumeAtStartOfOrder) * (volPct/100);

 if (qtyToExecute &lt;= 0) {
  printf ("KNICKS!!!!\n");
 }
 printf("\n %f qtyToExecute\n",qtyToExecute);
 //
 //double val = getRounded(502);
 //              printf(" %f getRounded \n",val);

 double value= 602;    
 int val1 = getRounded1((int) value);
 printf(" %d getRounded \n",val1);

 /*
 * test
 char msg[2];
 int t=0;
 while ( t&lt;40 ---="" -="" -p2="" 0.01="" 10="" 1:="" 2:="" 3:="" 3="" 4:="" 4="" 5="" 6="" 7="" 8="" 9="" :="" a="" am="" are="" b="" buffer="" char="" charmon1="" charmon="" chars="" chopprecision="" cvar="=" d="" dead="" double="" else="" equal="" est="" expected="" f="" fabs="" floor="" foo="" foodoo="" fractpart="" if="" int="" intpart2="" intpart="" is="" month1="" month="" msg="" mycptr1="234" mydbl1="" mydbl="" mydl1="" mydl="" myint="" n="" no="" not="" now="" on="" one="=" onechanged="" p1="" p2="" p3="" p4="" plus="" printf="" rand="" rand_max="%f" random="" s="" sizeof="" sprintf="" string:strlen="" strlen="" t:="" t="" temp="" test10="" test2:="" test8:="" test9="" test="" testing="" the="" they="" time="" toadd="" two="" twochanged="" wait_time="" was="" yes=""&gt; p4 %f \n",p3,p4);
 }

 if (LT_double(p3,p4)) {
  printf ( "test10  --- p3 %f &lt; p4 %f \n",p3,p4);
 }

 /* Test 11 */

 double p5=65.54;
 printf ( "\n\n test11  was p5 %f now is chopPrecision(p5) %f \n",p5,chopPrecision(p5));

 double e1,e2,e3,e4,e5,e6,e7=0;

 /* Test 12 */
 char str[7];
 sprintf(str,"%s","Test");

 if (strstr(str,"est")) {
  printf("test 12 got a match\n");
 }
 else {
  printf("test12 no match \n");
 }


 /* test 13 */
 printf ("test13 Pctage   %f\n",((39.05-39.01)/39.05 ) * 100 );

 /* test 14 */
 double foo23= 0.021234;
 char cstr14[5];
 sprintf (cstr14,"%.2f\n",fabs(foo23));
 double fooresult=atof(cstr14);

 printf ("test14 2. deviation   %f\n",fooresult);

 /*test 15 */
 int num=0;
 2 &gt; 4 ? num=1 :num =2;
 printf ("test 15:num is %d \n",num);

 /*test 16 */

 int dlbarr[]= {1,2,3,4,5};

 printf("test16 size is %d\n", sizeof(dlbarr));

 /*** test 17 ***********/

 ptrDataPoint2 = (DATAPOINTS2*) malloc(sizeof(*ptrDataPoint2));
 //simulate Get_UserPointer
 char *cptr = reinterpret_cast(ptrDataPoint2);

 ptrDataPoint2-&gt;priceCurve[0]=25;
 ptrDataPoint2-&gt;priceCurve[1]=20;
 ptrDataPoint2-&gt;priceCurve[2]=18;
 ptrDataPoint2-&gt;priceCurve[3]=17;
 ptrDataPoint2-&gt;priceCurve[4]=20;
 ptrDataPoint2-&gt;priceCurve[5]=21;
 ptrDataPoint2-&gt;priceCurve[6]=17;
 ptrDataPoint2-&gt;priceCurve[4]=16;
 ptrDataPoint2-&gt;priceCurve[7]=14;
 ptrDataPoint2-&gt;index=7;

 printf("test17:mixed but major down trend direction:getPriceDirection() --&gt;\n");
 getPriceDirection(cptr);



 ptrDataPoint2-&gt;priceCurve[0]=25;
 ptrDataPoint2-&gt;priceCurve[1]=20;
 ptrDataPoint2-&gt;priceCurve[2]=18;
 ptrDataPoint2-&gt;priceCurve[3]=17;
 ptrDataPoint2-&gt;priceCurve[4]=16;
 ptrDataPoint2-&gt;priceCurve[5]=15;
 ptrDataPoint2-&gt;priceCurve[6]=14;
 ptrDataPoint2-&gt;priceCurve[4]=13;
 ptrDataPoint2-&gt;priceCurve[7]=12;

 printf("test17 :Pure down trend direction:getPriceDirection() --&gt; \n");
 getPriceDirection(cptr);


 ptrDataPoint2-&gt;priceCurve[0]=14;
 ptrDataPoint2-&gt;priceCurve[1]=16;
 ptrDataPoint2-&gt;priceCurve[2]=17;
 ptrDataPoint2-&gt;priceCurve[3]=18;
 ptrDataPoint2-&gt;priceCurve[4]=20;
 ptrDataPoint2-&gt;priceCurve[5]=21;
 ptrDataPoint2-&gt;priceCurve[6]=24;
 ptrDataPoint2-&gt;priceCurve[4]=28;
 ptrDataPoint2-&gt;priceCurve[7]=29;


 printf("test17 : Pure upTrend direction:getPriceDirection() --&gt; \n");
 getPriceDirection(cptr);


 ptrDataPoint2-&gt;priceCurve[0]=14;
 ptrDataPoint2-&gt;priceCurve[1]=16;
 ptrDataPoint2-&gt;priceCurve[2]=17;
 ptrDataPoint2-&gt;priceCurve[3]=18;
 ptrDataPoint2-&gt;priceCurve[4]=10;
 ptrDataPoint2-&gt;priceCurve[5]=14;
 ptrDataPoint2-&gt;priceCurve[6]=17;
 ptrDataPoint2-&gt;priceCurve[4]=21;
 ptrDataPoint2-&gt;priceCurve[7]=22;


 printf("test17 : mixed but major upTrend direction: getPriceDirection() --&gt; \n");
 getPriceDirection(cptr);


 ptrDataPoint2-&gt;priceCurve[0]=61.76;
 ptrDataPoint2-&gt;priceCurve[1]=61.76;
 ptrDataPoint2-&gt;priceCurve[2]=61.85;
 ptrDataPoint2-&gt;priceCurve[3]=61.84;
 ptrDataPoint2-&gt;priceCurve[4]=61.75;
 ptrDataPoint2-&gt;priceCurve[5]=61.80;
 ptrDataPoint2-&gt;priceCurve[6]=61.79;
 ptrDataPoint2-&gt;priceCurve[4]=61.76;
 ptrDataPoint2-&gt;priceCurve[7]=61.75;

 printf("test17 : mixed --&gt;getPriceDirection() \n");
 getPriceDirection(cptr);

 free(cptr);

 /*** test 18****/

 printf("test18 :%d \n",123423 % 1000000);
 printf("test18 :%d \n",123423 % 100000);
 printf("test18 :%d \n",123423 % 10000);
 printf("test18 :%d \n",123423 % 10000);

 /*** test 19****/

 char stet[]="152345-152623";
 char * ptr_stet;

 printf("test19 strlen stet %d \n",strlen(stet));
 ptr_stet = strtok(stet,"-");
 while(ptr_stet!=NULL) {
  printf("test19 time:%f \n",atof(ptr_stet));
  ptr_stet = strtok(NULL,"-");

 }

 /**Test 20 ****/
 time_t now;
 time(&amp;now);
 struct tm * timeNow = localtime(&amp;now);
 mktime(timeNow);


 int startTime = 120000;
 //int startTime = 155800;
 int endTime = 121000;
 int numSlices= 5;
 int unloadTime = 500;

 int currentTime =timeNow-&gt;tm_hour*10000+timeNow-&gt;tm_min*100+timeNow-&gt;tm_sec;
 timeNow-&gt;tm_sec += 900;

 mktime(timeNow);

 int currentTimePlus15Min =timeNow-&gt;tm_hour*10000+timeNow-&gt;tm_min*100+timeNow-&gt;tm_sec;
 int currentTimeAsInt = currentTime/10000*3600 + currentTime/100*60+ currentTime%100;
 int start = startTime/10000*3600 + startTime/100*60+ startTime%100;
 int end = endTime/10000*3600 + endTime/100*60 + endTime%100;
 int temp1 = endTime/10000*3600 + endTime/100%100*60 + endTime%100- unloadTime;
 int temp2 =temp1/3600*10000+(temp1/60)%60*100+temp1%60;

 int nextTimeToSendSlice = 121020+300;
 int nextTimeToSendSlice2 = nextTimeToSendSlice/10000*3600 + nextTimeToSendSlice/100*60+nextTimeToSendSlice%100;


 if (nextTimeToSendSlice%100&gt;=60){
  nextTimeToSendSlice+=100;
  nextTimeToSendSlice-=60;
  printf("1. test20: currentTime %d currentTimePlus15Min %d currentTimeAsInt %d start %d end %d end-start %d SlicesTimeApart %d temp1 %d temp2 %d nextTimetoSendSlice%d\n\n", currentTime,currentTimePlus15Min,currentTimeAsInt,start,end,(end-start),(end-start)/numSlices,temp1,temp2,nextTimeToSendSlice);

 }

 if (nextTimeToSendSlice%10000&gt;=6000){

  nextTimeToSendSlice+=10000;
  nextTimeToSendSlice-=6000;
  printf("2. test20: currentTime %d currentTimePlus15Min %d currentTimeAsInt %d start %d end %d end-start %d SlicesTimeApart %d temp1 %d temp2%d nextTimeToSendSlice %d\n\n", currentTime,currentTimePlus15Min,currentTimeAsInt,start,end,(end-start),(end-start)/numSlices,temp1,temp2,nextTimeToSendSlice);

 }

 printf("3. test20: currentTime %d currentTimePlus15Min %d currentTimeAsInt %d start %d end %d end-start %d SlicesTimeApart %d temp1 %d temp2%d nextTimeToSendSlice %d nextTimeToSendSlice2 %d \n\n", currentTime,currentTimePlus15Min,currentTimeAsInt,start,end,(end-start),(end-start)/numSlices,temp1,temp2,nextTimeToSendSlice,nextTimeToSendSlice2);

 /*************** test 21 ****************************/
 tm tmpTime= tm();

 tmpTime.tm_sec=57;
 tmpTime.tm_min=41;
 tmpTime.tm_hour=14;
 tmpTime.tm_mday=2;
 tmpTime.tm_mon= 1;
 tmpTime.tm_year=2006-1900;

 time_t myTimeT = mktime(&amp;tmpTime);


 tm tmpTime1= tm();
 tmpTime1.tm_sec=02;
 tmpTime1.tm_min=42;
 tmpTime1.tm_hour=14;
 tmpTime1.tm_mday=2;
 tmpTime1.tm_mon= 1;
 tmpTime1.tm_year=2006-1900;

 time_t myTimeT2 = mktime(&amp;tmpTime1);

 printf(" test21: the diff %f \n\n\n",difftime(myTimeT2,myTimeT));

 /********** test 22 ***********/

 int st1=150000;
 //int subtractsecs= 30;
 int subtractsecs= 30;

 int st2=154500;
 int st3=154607;

 //int st4=154558;
 //int addsecs =35;

 int st4=150000;
 //int addsecs =35;
 int addsecs =300;

 printf("test22:1 :st1 %d  subtractSeconds %d subtractSecondsfromTime %d \n",st1,subtractsecs,subtractSecondsFromTime(st1,subtractsecs));

 /****************** test 24 *************/

 time_t now123;
 time(&amp;now123);

 struct tm * timeNow123 = localtime(&amp;now123);
 mktime(timeNow123);

 int currentTime123 =timeNow123-&gt;tm_hour*10000+timeNow123-&gt;tm_min*100+timeNow123-&gt;tm_sec;
 int current123 = currentTime123/10000*3600 + currentTime123/100*60+currentTime123%100;

 printf("test 24 timeNow123-&gt;tm_hour %d,timeNow123-&gt;tm_hour*10000 %d,timeNow123-&gt;tm_min %d,timeNow123-&gt;tm_min*100 %d,timeNow123-&gt;tm_sec %d \n",

  timeNow123-&gt;tm_hour,timeNow123-&gt;tm_hour*10000,timeNow123-&gt;tm_min,timeNow123-&gt;tm_min*100,timeNow123-&gt;tm_sec);

 printf("test 24 currentTime123 %d \n",currentTime123);
 /*
 int startTime=
 int endTime =
 int end = endTime/10000*3600 + endTime/100*60+endTime%100;
 int start = startTime/10000*3600 + startTime/100*60+startTime%100;
 */

 /*********************test 25 ***************/
 /*
 char stet[]="152345-152623";
 char * ptr_stet;

 printf("test19 strlen stet %d \n",strlen(stet));
 ptr_stet = strtok(stet,"-");
 while(ptr_stet!=NULL) {
 printf("test19 time:%f \n",atof(ptr_stet));
 ptr_stet = strtok(NULL,"-");

 }  

 */

 char  strfoo[] ="34.58-10000-34.89-250000-36.78-24000";
 char * ptr_strfoo=NULL;

 ptr_strfoo = strtok(strfoo,"-");


 //printf("test25  1:%f \n",atof(ptr_strfoo));
 //printf("test 25 strlen ptr_strfoo %d\n",strlen(ptr_strfoo));

 while(ptr_strfoo != NULL) {
  /*
  char pxvol[strlen(ptr_strfoo)];
  strcpy(pxvol,ptr_strfoo);
  char * ptr_pxvol=NULL;
  ptr_pxvol= strtok(pxvol,":");

  while(ptr_pxvol != NULL) {
  printf("test25 -----b Value--&gt;%f \n",atof(ptr_pxvol));
  ptr_pxvol= strtok(NULL,":");
  }
  */
  /* Below work
  printf("test25  Price:%s \n",ptr_strfoo);
  ptr_strfoo = strtok(NULL,"-");
  printf("test25  Qty :%s \n",ptr_strfoo);
  ptr_strfoo = strtok(NULL,"-");
  */

  printf("test25  Price:%f \n",atof(ptr_strfoo));
  ptr_strfoo = strtok(NULL,"-");
  printf("test25  Qty :%f \n",atof(ptr_strfoo));
  ptr_strfoo = strtok(NULL,"-");
 }   


 /*********** test 26 **********/
 char * fooarr[5];
 fooarr[0]= (char*)malloc(100);
 strcpy(fooarr[0],"1 This is test26.Array of char ptrs");

 fooarr[1]= (char*)malloc(100);
 strcpy(fooarr[1],"2 test 26 .Box car racers ar here");

 fooarr[2]= (char*)malloc(100);
 strcpy(fooarr[2],"3 test 26 .Box car racers ar here");

 fooarr[3]= (char*)malloc(100);
 strcpy(fooarr[3],"4. test 26 .Box car racers ar here");

 fooarr[4]= (char*)malloc(100);
 strcpy(fooarr[4],"5. test 26 .Box car racers ar here");

 fooarr[5]= (char*)malloc(100);
 strcpy(fooarr[5],"6. test 26 .Box car racers ar here");
/* Need to malloc more space or else below will run out of space
 for (int x =0; x&lt; 5;x++) {

  printf("test26  fooarr x %d %s\n ",x,fooarr[x]);
 }
 */

 free(*fooarr);

 /*********** test 27 **********/

 /*** test 27****/

 char symstr[]="ABC.O";
 char * place;

 place = strtok(symstr,".");

 printf("test27 1 time:%s \n",place);
 place = strtok(NULL,".");
 printf("test27 2 time:%s \n",place);

 /*********** test 28 **********/


 double price1 = 28.75;
 double price2 = 28.7589;      
 double price3 = 28.7524;      
 double price4 = 28.7; 
 printf("test28 price %f  roundPrice %f\n",price1,roundPrice("buy",price1));
 printf("test28 price %f  roundPrice %f\n",price2,roundPrice("buy",price2));
 printf("test28 price %f  roundPrice %f\n",price3,roundPrice("buy",price3));
 printf("test28 price %f  roundPrice %f\n",price4,roundPrice("buy",price4));

 /*********** test 29 **********/

 string sfoo = "5000@20=100000";
 getSmartExpStr(sfoo);

 /*********** test 30 **********/

 //              string sf=getExpStr(500,20.2);


 //              printf("getExpStr %s\n",sf.c_str());
 string sf= ToString<int>(42); 
 printf("test 30 sf %s\n",sf.c_str());

 string p1977=getExpStr(1000000,180.98);
 printf("test 30 getExpStr %s\n",p1977.c_str());

 p1977=getExpStr(500,20.2);
 printf("test 30 getExpStr %s\n",p1977.c_str());

 /*********** test 31 **********/

 printf ("test 31 percentd %d \n",25/2);
 printf ("test 31 percentf %f \n",25/2);
 printf ("test 31 percentfABS %f \n",abs(25/2));

 /*********** test 32 **********/

 double * ptr_double = testPtrReturn();
 printf("test 32 ptr_double :testPtrReturn() %f\n", *ptr_double);

 double  ptr_double2 = *testPtrReturn();
 printf("test 32 ptr_double2 :testPtrReturn()  %f\n", ptr_double2);


 /*********** test 34 **********/

 //printf("testing 34 ref return  %s\n", testStrRefRtrn());

 /*********** test 35 **********/

 A aobj;
 A* aptr = new A();
 A&amp; aref=(*aptr);
 delete (aptr);

 /*********** test 35 **********/
 //FOO foo foo

 class Ins {
 public:
  virtual void foo() {
   printf("Test 35 : class Ins: foo \n");
  }
  virtual ~Ins(){}

 };

 class fx : public Ins {
 public:
  virtual void foo() {
   printf("Test 35 : class fx: foo \n");
  }

  virtual ~fx(){}
 };

 Ins *iptr = new Ins();
 fx * fptr = new fx();
 iptr=&amp;(*fptr);

 iptr-&gt;foo();
 delete (iptr); // fptr will be gone as wellss
 //delete (fptr);


 /***** test 37************/


 MZSingleton *myptrz = MZSingleton::getMZSingleton1();
 MZSingleton *myptrz2 = myptrz-&gt;getMZSingleton1();
 //                        MZSingleton &amp; myrefz =MZSingleton::getMZSingleton1();
 // Singleton *p2 = p1-&gt;Instance();
 // Singleton &amp; ref = * Singleton::Instance();
 
/*** test 38  create thread argument struct for thr_func() ***/

  pthread_t thr[NUM_THREADS];
  int i, rc;
  /* create a thread_data_t argument array */
  thread_data_t thr_data[NUM_THREADS];
 
  /* create threads */
  for (i = 0; i &lt; NUM_THREADS; ++i) {
    thr_data[i].tid = i;
    if ((rc = pthread_create(&amp;thr[i], NULL, thr_func, &amp;thr_data[i]))) {
      fprintf(stderr, "Test38:test38: error: pthread_create, rc: %d\n", rc);
      return EXIT_FAILURE;
    }
  }
  /* block until all threads complete */
  for (i = 0; i &lt; NUM_THREADS; ++i) {
    pthread_join(thr[i], NULL);
  }
 
 return 0;
}


</int></int></math></pre>
</div>
</div>
<div class="post-footer">
<div class="post-footer-line post-footer-line-1">
<span class="post-author vcard">
Posted by
<span class="fn" itemprop="author" itemscope="itemscope" itemtype="http://schema.org/Person">

<a class="g-profile" data-gapiattached="true" data-gapiscan="true" data-onload="true" href="https://www.blogger.com/profile/01803291076927856539" rel="author" title="author profile">
<span itemprop="name">MZSysEngineer</span>
</a>
</span>
</span>
<span class="post-timestamp">
at

<a class="timestamp-link" href="https://mzsysengineer.blogspot.com/2016/08/test2cpp-with-threads-basic.html" rel="bookmark" title="permanent link"><abbr class="published" itemprop="datePublished" title="2016-08-26T12:08:00-07:00">12:08 PM</abbr></a>
</span>
<span class="reaction-buttons">
</span>
<span class="post-comment-link">
</span>
<span class="post-backlinks post-comment-link">
</span>
<span class="post-icons">

</span>
<div class="post-share-buttons goog-inline-block">
</div>
</div>
<div class="post-footer-line post-footer-line-2">
<span class="post-labels">
</span>
</div>
<div class="post-footer-line post-footer-line-3">
<span class="post-location">
</span>
</div>
</div>
</div>
<div class="comments" id="comments">
<a href="https://www.blogger.com/u/2/null" name="comments"></a>
<h4>
No comments:</h4>
<div id="Blog1_comments-block-wrapper">

</div>
<div class="comment-footer">
</div>
<div class="comment-form">
<a href="https://www.blogger.com/u/2/null" name="comment-form"></a>
<h4 id="comment-post-message">
Post a Comment</h4>
</div>
<div id="backlinks-container">
<div id="Blog1_backlinks-container">
</div>
</div>
</div>
</div>
</div>
</div>
</div>
<div class="blog-pager" id="blog-pager">
<a class="home-link" href="https://mzsysengineer.blogspot.com/">Home</a>
</div>
<div class="post-feeds">
</div>
</div>
</div>
</div>
</div>
<div class="column-left-outer">
<div class="column-left-inner">
<aside>
</aside>
</div>
</div>
<div class="column-right-outer">
<div class="column-right-inner">
<aside>
<div class="sidebar section" id="sidebar-right-1">
<div class="widget Followers" data-version="1" id="Followers1">
<h2 class="title">
Followers</h2>
<div class="widget-content">
<div id="Followers1-wrapper">
<div style="margin-right: 2px;">
<div>

</div>
</div>
</div>
<span class="widget-item-control">

</span>

</div>
</div>
<div class="widget BlogArchive" data-version="1" id="BlogArchive1">
<h2>
Blog Archive</h2>
<div class="widget-content">
<div id="ArchiveList">
<div id="BlogArchive1_ArchiveList">
<ul class="hierarchy">
<li class="archivedate collapsed">
<a class="toggle" href="https://www.blogger.com/u/2/null">
<span class="zippy">

        ►&nbsp;
      
</span>
</a>
<a class="post-count-link" href="https://mzsysengineer.blogspot.com/2018/">
2018
</a>
<span class="post-count" dir="ltr">(41)</span>











</li>
</ul>
<ul class="hierarchy">
<li class="archivedate collapsed">
<a class="toggle" href="https://www.blogger.com/u/2/null">
<span class="zippy">

        ►&nbsp;
      
</span>
</a>
<a class="post-count-link" href="https://mzsysengineer.blogspot.com/2017/">
2017
</a>
<span class="post-count" dir="ltr">(14)</span>








</li>
</ul>
<ul class="hierarchy">
<li class="archivedate collapsed">
<a class="toggle" href="https://www.blogger.com/u/2/null">
<span class="zippy">

        ►&nbsp;
      
</span>
</a>
<a class="post-count-link" href="https://mzsysengineer.blogspot.com/2016/">
2016
</a>
<span class="post-count" dir="ltr">(12)</span>







</li>
</ul>
<ul class="hierarchy">
<li class="archivedate collapsed">
<a class="toggle" href="https://www.blogger.com/u/2/null">
<span class="zippy">

        ►&nbsp;
      
</span>
</a>
<a class="post-count-link" href="https://mzsysengineer.blogspot.com/2015/">
2015
</a>
<span class="post-count" dir="ltr">(11)</span>





</li>
</ul>
<ul class="hierarchy">
<li class="archivedate collapsed">
<a class="toggle" href="https://www.blogger.com/u/2/null">
<span class="zippy">

        ►&nbsp;
      
</span>
</a>
<a class="post-count-link" href="https://mzsysengineer.blogspot.com/2014/">
2014
</a>
<span class="post-count" dir="ltr">(5)</span>




</li>
</ul>
<ul class="hierarchy">
<li class="archivedate collapsed">
<a class="toggle" href="https://www.blogger.com/u/2/null">
<span class="zippy">

        ►&nbsp;
      
</span>
</a>
<a class="post-count-link" href="https://mzsysengineer.blogspot.com/2013/">
2013
</a>
<span class="post-count" dir="ltr">(11)</span>





</li>
</ul>
<ul class="hierarchy">
<li class="archivedate collapsed">
<a class="toggle" href="https://www.blogger.com/u/2/null">
<span class="zippy">

        ►&nbsp;
      
</span>
</a>
<a class="post-count-link" href="https://mzsysengineer.blogspot.com/2012/">
2012
</a>
<span class="post-count" dir="ltr">(8)</span>





</li>
</ul>
<ul class="hierarchy">
<li class="archivedate collapsed">
<a class="toggle" href="https://www.blogger.com/u/2/null">
<span class="zippy">

        ►&nbsp;
      
</span>
</a>
<a class="post-count-link" href="https://mzsysengineer.blogspot.com/2011/">
2011
</a>
<span class="post-count" dir="ltr">(2)</span>

</li>
</ul>
</div>
</div>
<span class="widget-item-control">

</span>

</div>
</div>
<div class="widget Profile" data-version="1" id="Profile1">
<h2>
About Me</h2>
<div class="widget-content">
<dl class="profile-datablock">
<dt class="profile-data">
<a class="profile-name-link g-profile" data-gapiattached="true" data-gapiscan="true" data-onload="true" href="https://www.blogger.com/profile/01803291076927856539" rel="author" style="background-image: url(//www.blogger.com/img/logo-16.png);">
MZSysEngineer
</a>
</dt>
</dl>
<a class="profile-link" href="https://www.blogger.com/profile/01803291076927856539" rel="author">View my complete profile</a>

<span class="widget-item-control">

</span>

</div>
</div>
</div>
</aside>
</div>
</div>
</div>
</div>
</div>
</div>
<div class="main-cap-bottom cap-bottom">


</div>
</div>
<footer>
<div class="footer-outer">
<div class="footer-cap-top cap-top">


</div>
<div class="fauxborder-left footer-fauxborder-left">

<div class="region-inner footer-inner">

<table border="0" cellpadding="0" cellspacing="0" class="section-columns columns-2">
<tbody>
<tr>
<td class="first columns-cell">

</td>
<td class="columns-cell">

</td>
</tr>
</tbody>
</table>
<div class="foot section" id="footer-3" name="Footer">
<div class="widget Attribution" data-version="1" id="Attribution1">
<div class="widget-content" style="text-align: center;">
Awesome Inc. theme. Powered by <a href="https://www.blogger.com/" target="_blank">Blogger</a>.
</div>
<span class="widget-item-control">

</span>

</div>
</div>
</div>
</div>
<div class="footer-cap-bottom cap-bottom">


</div>
</div>
</footer>

</div>
</div>
<div class="content-cap-bottom cap-bottom">


</div>
</div>
</div>
<div class="blogger-clickTrap singleton-element" style="background: white none repeat scroll 0% 0%; cursor: default; height: 100%; left: 0px; opacity: 0.01; position: fixed; top: 0px; width: 100%; z-index: 1000;">
&nbsp;</div>
<div class="singleton-element" style="background-color: black; border: 1px solid rgb(170, 170, 170); color: white; font-family: &quot;trebuchet ms&quot;, verdana, arial, sans-serif; font-size: 24px; height: 28px; left: -225px; margin: 0px; opacity: 0.5; padding: 10px; position: absolute; text-align: center; top: 75px; transform-origin: 50% 0px 0px; transform: rotate(-45deg); width: 600px; z-index: 1200;">
Preview</div>
<div class="singleton-element" style="border: 1px solid transparent; color: white; font-family: &quot;trebuchet ms&quot;, verdana, arial, sans-serif; font-size: 24px; height: 28px; left: -225px; margin: 0px; padding: 10px; position: absolute; text-align: center; top: 75px; transform-origin: 50% 0px 0px; transform: rotate(-45deg); width: 600px; z-index: 1200;">
Preview</div>
<html>
<body>
<pre>
#include <stdio .h="">
#include <string>
#include <sstream>
#include <math .h="">
#include <time .h="">
#include <ctime>
#include <string .h="">
#include <cstdlib>
#include <pthread .h="">
 
#define NUM_THREADS 20

using namespace std;

int subtractSecondsFromTime( int, int);
//int getSecondsBetweenTime ( int,int );
//int addSecondsToTime(int,int );
int subtractSecondsFromTime(int ,int );
int addSecondsToTime(int ,int );
int copychar (const char* , char* );

void getPriceDirection(char *p);
double getRounded(double);
int    getRounded1(int);

int EQ_double(double , double );
int GT_double(double , double );
int LT_double(double , double );
double chopPrecision (double);

void getSmartExpStr(string );

string getExpStr(int,double);

template<class i="">
string ToString(const I&amp;);

/********/
const int POINTS= 25; //from 0 to 25 = 26 ( 9:30 = OPEN, 945,1000 .... 1545)
typedef struct {
 double priceCurve[POINTS];
 int index;
 int first;
} DATAPOINTS2;

DATAPOINTS2* ptrDataPoint2;
 
typedef struct {
 int qty;
 double price;
 double notional;
} SMART_EXP_STR;

SMART_EXP_STR* smartExpStr;

double roundPrice(string , double );
double *  testPtrReturn();
string&amp; testStrRefRtrn();

/********/
class A {
public:
 A() {
  printf ("In Ctor of A \n");
 }

};
/********/
class MZSingleton {

public:
 static MZSingleton* getMZSingleton1();
 static MZSingleton* getMZSingleton2();
protected:
 MZSingleton();
 MZSingleton(const MZSingleton&amp;);
 MZSingleton&amp; operator=(const MZSingleton&amp;);
private:
 static MZSingleton* MZSingleton_ptr;

};


MZSingleton* MZSingleton::MZSingleton_ptr=0;// initialize pointer

MZSingleton* MZSingleton::getMZSingleton1()
{
 if (MZSingleton_ptr == 0)  // is it the first call?
 {
  MZSingleton_ptr = new MZSingleton; // create sole instance
 }
 return MZSingleton_ptr; // address of sole instance
}
MZSingleton* MZSingleton::getMZSingleton2()
{
 static MZSingleton MZSingleton_ref;
 return &amp;MZSingleton_ref;
}
MZSingleton::MZSingleton()
{

}


/********/
/*
string&amp; testStrRefRtrn() {
string  &amp;strt123;
strt123 = new string("foo");
return strt123;
}
*/
double* testPtrReturn() {
 double fo = 23.45;

 double* p=&amp;fo;


 return p;

 //      double w      = 880.24;
 //      double *ws    = &amp;w;
 //      double **wwss = &amp;ws;

 //      return wwss;

}

string getExpStr(int qty, double price ) {

 double notional = qty*price;

 char str[50];

 //sprintf_s(str,"%-20.4f",notional);
 sprintf(str,"%-20.4f",notional);
 

 string retstr;
 retstr =  ToString<int>(qty);
 retstr += "@$";
 retstr += ToString<double>(price);
 retstr += "=$";
 //retstr += ToString<double>(notional);
 retstr += str;

 return retstr;

}
template<class t="">
std::string ToString(const T&amp; val)
{
 std::stringstream strm;
 strm &lt;&lt; val;
 return strm.str();
}

void setSmartExpStr(SMART_EXP_STR* sPtr,int qty,double px,double notional) {

 sPtr = (SMART_EXP_STR*) malloc(sizeof(*sPtr));

 sPtr-&gt;qty=qty;
 sPtr-&gt;price=px;
 sPtr-&gt;notional=notional;

}

//SMART_EXP_STR* getSmartExpStr(string smartExpStr) {

void getSmartExpStr(string smartExpStr) {
 //char stet[]="152345-152623";
 //
 //
 //

 //int len = strlen(smartExpStr.c_str());
 char *stet= const_cast<char> (smartExpStr.c_str());
 char * ptr_stet;

 printf("test29 strlen stet %d \n",strlen(stet));
 ptr_stet = strtok(stet,"@");
 printf("test29 Qty:%d \n",atoi(ptr_stet));
 ptr_stet = strtok(NULL,"@");
 printf("test29 px:%f \n",atoi(ptr_stet));

}

double roundPrice(string side, double price){
 double returnPrice = -1;
 if (side=="buy"){
  double tmp1 = 100*price;
  int tmp2 = (int)tmp1;
  double buying = (double)(tmp2)/100;
  returnPrice = buying;
 }else{
  double tmp3 = price + 0.009;
  double tmp4 = 100*tmp3;
  int tmp5 = (int)tmp4;
  double selling = ((double)tmp5)/100;
  returnPrice = selling;
 }
 return returnPrice;
}

int subtractSecondsFromTime(int time1,int secs){
 int temp = time1/10000*3600  + (time1%10000)/100*60 + time1%100;
 temp-=secs;
 return temp/3600*10000  +   temp%3600/60*100 +  temp%60;
}

int addSecondsToTime(int time1,int secs){
 int temp = time1/10000*3600+  (time1%10000)/100*60 + time1%100;
 temp+=secs;
 return temp/3600*10000+temp%3600/60*100+temp%60;
}

/*
// pass in 123425,29 (will subtract 29 seconds from 123425)
// return 123356
int subtractSecondsFromTime( int timeHHMMSS,int seconds) {
time_t now;
time(&amp;now);
struct tm * currentTimeNow = localtime(&amp;now);
mktime(currentTimeNow);

char strtimeHHMMSS[6];
sprintf(strtimeHHMMSS,"%d",timeHHMMSS);

char hour[2];
char min[2];
char sec[2];

hour[0]=strtimeHHMMSS[0];
hour[1]=strtimeHHMMSS[1];
hour[2] ='\0';
int hourint = atoi(hour);

if (hourint == 0 || hourint &gt; 23 || hourint <10 -1="" if="" int="" min="" minint="" return="" strtimehhmmss=""> 59) {
return -1;
}

sec[0]=strtimeHHMMSS[4];
sec[1]=strtimeHHMMSS[5];
sec[2] ='\0';
int secint = atoi(sec);
if (secint <0 secint=""> 59) {
return -1;
}


tm newTime = tm();
newTime.tm_sec=secint;
newTime.tm_min=minint;
newTime.tm_hour=hourint;
newTime.tm_mday=currentTimeNow-&gt;tm_mday;
newTime.tm_mon= currentTimeNow-&gt;tm_mon;
newTime.tm_year=currentTimeNow-&gt;tm_year;

time_t myTimeT = mktime(&amp;newTime);
newTime.tm_sec = newTime.tm_sec - seconds;
newTime.tm_min = newTime.tm_min;
mktime(&amp;newTime);

printf ("tm_min tim_min %d tim_min*100 %d \n",newTime.tm_min,newTime.tm_min*100);
int timeMinusSeconds =newTime.tm_hour*10000 +  newTime.tm_min*100 + newTime.tm_sec;
}
*/

// pass in 123356,123429
// return 34
int getSecondsBetweenTime ( int time1HHMMSS,int time2HHMMSS) {
 time_t now;
 time(&amp;now);
 struct tm * currentTimeNow = localtime(&amp;now);
 mktime(currentTimeNow);

 char strtimeHHMMSS[6];
 sprintf(strtimeHHMMSS,"%d",time1HHMMSS);

 char hour[2];
 char min[2];
 char sec[2];

 hour[0]=strtimeHHMMSS[0];
 hour[1]=strtimeHHMMSS[1];
 hour[2] ='\0';
 int hourint = atoi(hour);

 if (hourint == 0 || hourint &gt; 24 || hourint &lt; 10) {
  return -1;
 }

 min[0]=strtimeHHMMSS[2];
 min[1]=strtimeHHMMSS[3];
 min[2] ='\0';
 int minint = atoi(min);

 sec[0]=strtimeHHMMSS[4];
 sec[1]=strtimeHHMMSS[5];
 sec[2] ='\0';
 int secint = atoi(sec);

 tm newTime1 = tm();
 newTime1.tm_sec=secint;
 newTime1.tm_min=minint;
 newTime1.tm_hour=hourint;
 newTime1.tm_mday=currentTimeNow-&gt;tm_mday;
 newTime1.tm_mon= currentTimeNow-&gt;tm_mon;
 newTime1.tm_year=currentTimeNow-&gt;tm_year;

 time_t mynewTimeT1 = mktime(&amp;newTime1);




 char strtime2HHMMSS[6];
 //sprintf(strtime2HHMMSS,"%d",time2HHMMSS);
 sprintf(strtime2HHMMSS,"%d",time2HHMMSS);

 char hour2[2];
 char min2[2];
 char sec2[2];

 hour2[0]=strtime2HHMMSS[0];
 hour2[1]=strtime2HHMMSS[1];
 hour2[2] ='\0';
 int hourint2 = atoi(hour2);

 min2[0]=strtime2HHMMSS[2];
 min2[1]=strtime2HHMMSS[3];
 min2[2] ='\0';
 int minint2 = atoi(min2);

 sec2[0]=strtime2HHMMSS[4];
 sec2[1]=strtime2HHMMSS[5];
 sec2[2] ='\0';
 int secint2 = atoi(sec2);

 tm newTime2 = tm();
 newTime2.tm_sec=secint2;
 newTime2.tm_min=minint2;
 newTime2.tm_hour=hourint2;
 newTime2.tm_mday=currentTimeNow-&gt;tm_mday;
 newTime2.tm_mon= currentTimeNow-&gt;tm_mon;
 newTime2.tm_year=currentTimeNow-&gt;tm_year;

 time_t mynewTimeT2 = mktime(&amp;newTime2);
 return (int) difftime(mynewTimeT2,mynewTimeT1);

}
/*
//pass in 123356,34 (will add 34 seconds to 123356)
//return  123429
int addSecondsToTime(int timeHHMMSS,int seconds){

time_t now;
time(&amp;now);
struct tm * currentTimeNow = localtime(&amp;now);
mktime(currentTimeNow);

char strtimeHHMMSS[6];
sprintf(strtimeHHMMSS,"%d",timeHHMMSS);

char hour[2];
char min[2];
char sec[2];

hour[0]=strtimeHHMMSS[0];
hour[1]=strtimeHHMMSS[1];
hour[2] ='\0';
int hourint = atoi(hour);

if (hourint == 0 || hourint &gt; 24 || hourint &lt; 10) {
return -1;
}

min[0]=strtimeHHMMSS[2];
min[1]=strtimeHHMMSS[3];
min[2] ='\0';
int minint = atoi(min);
if (minint <0 minint=""> 60) {
return -1;
}

sec[0]=strtimeHHMMSS[4];
sec[1]=strtimeHHMMSS[5];
sec[2] ='\0';
int secint = atoi(sec);
if (secint <0 secint=""> 60) {
return -1;
}


tm newTime = tm();
newTime.tm_sec=secint;
newTime.tm_min=minint;
newTime.tm_hour=hourint;
newTime.tm_mday=currentTimeNow-&gt;tm_mday;
newTime.tm_mon= currentTimeNow-&gt;tm_mon;
newTime.tm_year=currentTimeNow-&gt;tm_year;

time_t myTimeT = mktime(&amp;newTime);
newTime.tm_sec = newTime.tm_sec + seconds;
mktime(&amp;newTime);

int timePlusSeconds =newTime.tm_hour*10000 +  newTime.tm_min*100 + newTime.tm_sec;

return timePlusSeconds;
}
*/
//
// This will get the direction based off off all points in the structure
//
//

void getPriceDirection(char *p) {
 double *pCurve=NULL;
 int size= ((DATAPOINTS2*)p)-&gt;index;
 pCurve= ((DATAPOINTS2*)p)-&gt;priceCurve;

 int differencePtm1_Pt=0;
 int differencePt_Ptm1=0;
 double lnPricePoints_Pt_Ptm1=0.0;

 for (int x =0; x &lt; size; x++ ) {
  differencePtm1_Pt += (int)( pCurve[x] - pCurve[x+1]);
  differencePt_Ptm1 += (int)( pCurve[x+1] - pCurve[x]);
  //lnPricePoints_Pt_Ptm1 += log (abs((int) (pCurve[x+1] - pCurve[x])));
  lnPricePoints_Pt_Ptm1 += log (fabs((pCurve[x+1] - pCurve[x])));
 }
 printf("-----&gt; differencePt_Ptm1 %d  differencePtm1_Pt %d  lnPricePoints_Pt_Ptm1 %f \n\n ",
  differencePt_Ptm1,differencePtm1_Pt, lnPricePoints_Pt_Ptm1);
}






/*
double getRounded (double quantity) {
double remainder = (int) quantity / 100 ;

if (remainder &gt;= 50) {
double foo = quantity-remainder+100;
printf("        remainder %f returning %f\n",remainder,foo);
}
else {
double foo = quantity - remainder;                    
printf("        remainder %f returning %f\n",remainder,foo);
}
return (remainder &gt;= 50) ? quantity  - remainder + 100 : quantity-remainder
}
*/

//used to test if doubles are equal
int EQ_double ( double p1 , double p2 )
{
 if (fabs(p1 - p2) &lt;= 0.0001)  {
  printf ("test.c: EQ_double: p1 %f - p2 %f = %f &lt; 0.0001 \n",p1,p2,fabs(p1 - p2));
  return 1;
 }
 else {
  printf ("test.c: EQ_double: p1 %f - p2 %f = %f &gt; 0.0001 \n",p1,p2,fabs(p1 - p2));
  return 0;
 }
}

//used to test if doubles are equal
int GT_double ( double p1 , double p2 )
{
 if (p1&gt;p2)  return 1;
 else        return 0;
}
//used to test if doubles are equal
int LT_double ( double p1 , double p2 )
{
 if (p1 &lt; p2)  return 1;
 else          return 0;
}

//returns a large

int  getRounded1 (int quantity) {
 //return (quantity % 100 == 0);
 int remain= quantity % 100;
 return remain;
}

//Give 65.378765;
//return 65.37000 //non rounding, unlike sprintf
//
double chopPrecision(double p1) {

 double fractpart,intpart,intpart2;
 double one=1;
 fractpart=modf(p1,&amp;intpart);
 fractpart=modf(fractpart*100,&amp;intpart2);

 if(fractpart &gt; one  ||
  EQ_double(fractpart,1)) {
   printf ("   &gt;=1     routine:p1 %f fractpart %f\n",p1,fractpart);
   return p1;
 }
 else {
  printf ("   <1 --subtract="" -="" 38:="" ::::hello="" _thread_data_t="" arg="" argument="" create="" ctr="" d="" data-="" data="(thread_data_t" double="" f="" fabs="" for="" fractpart="" from="" function="" i="" id:="" int="" intpart2="" intpart="" n="" one-fractpart="" one="" p1="" printf="" return="" routine:p1="" struct="" stuff="" test38::::="" test="" thr_func="" thread="" thread_data_t="" tid="" typedef="" void="">tid,i);
 }
 
  pthread_exit(NULL);
}
 
int main() {
 static const char* SEP=",";
 printf("%s Seperator",SEP);

 /**** TEST *********/
 double currentVolume = 12000;
 double volumeAtStartOfOrder = 10000;
 double volPct = 33;

 double qtyToExecute =  (currentVolume - volumeAtStartOfOrder) * (volPct/100);

 if (qtyToExecute &lt;= 0) {
  printf ("KNICKS!!!!\n");
 }
 printf("\n %f qtyToExecute\n",qtyToExecute);
 //
 //double val = getRounded(502);
 //              printf(" %f getRounded \n",val);

 double value= 602;    
 int val1 = getRounded1((int) value);
 printf(" %d getRounded \n",val1);

 /*
 * test
 char msg[2];
 int t=0;
 while ( t<40 ---="" -="" -p2="" 0.01="" 10="" 1:="" 2:="" 3:="" 3="" 4:="" 4="" 5="" 6="" 7="" 8="" 9="" :="" a="" am="" are="" b="" buffer="" char="" charmon1="" charmon="" chars="" chopprecision="" cvar="=" d="" dead="" double="" else="" equal="" est="" expected="" f="" fabs="" floor="" foo="" foodoo="" fractpart="" if="" int="" intpart2="" intpart="" is="" month1="" month="" msg="" mycptr1="234" mydbl1="" mydbl="" mydl1="" mydl="" myint="" n="" no="" not="" now="" on="" one="=" onechanged="" p1="" p2="" p3="" p4="" plus="" printf="" rand="" rand_max="%f" random="" s="" sizeof="" sprintf="" string:strlen="" strlen="" t:="" t="" temp="" test10="" test2:="" test8:="" test9="" test="" testing="" the="" they="" time="" toadd="" two="" twochanged="" wait_time="" was="" yes=""> p4 %f \n",p3,p4);
 }

 if (LT_double(p3,p4)) {
  printf ( "test10  --- p3 %f &lt; p4 %f \n",p3,p4);
 }

 /* Test 11 */

 double p5=65.54;
 printf ( "\n\n test11  was p5 %f now is chopPrecision(p5) %f \n",p5,chopPrecision(p5));

 double e1,e2,e3,e4,e5,e6,e7=0;

 /* Test 12 */
 char str[7];
 sprintf(str,"%s","Test");

 if (strstr(str,"est")) {
  printf("test 12 got a match\n");
 }
 else {
  printf("test12 no match \n");
 }


 /* test 13 */
 printf ("test13 Pctage   %f\n",((39.05-39.01)/39.05 ) * 100 );

 /* test 14 */
 double foo23= 0.021234;
 char cstr14[5];
 sprintf (cstr14,"%.2f\n",fabs(foo23));
 double fooresult=atof(cstr14);

 printf ("test14 2. deviation   %f\n",fooresult);

 /*test 15 */
 int num=0;
 2 &gt; 4 ? num=1 :num =2;
 printf ("test 15:num is %d \n",num);

 /*test 16 */

 int dlbarr[]= {1,2,3,4,5};

 printf("test16 size is %d\n", sizeof(dlbarr));

 /*** test 17 ***********/

 ptrDataPoint2 = (DATAPOINTS2*) malloc(sizeof(*ptrDataPoint2));
 //simulate Get_UserPointer
 char *cptr = reinterpret_cast<char>(ptrDataPoint2);

 ptrDataPoint2-&gt;priceCurve[0]=25;
 ptrDataPoint2-&gt;priceCurve[1]=20;
 ptrDataPoint2-&gt;priceCurve[2]=18;
 ptrDataPoint2-&gt;priceCurve[3]=17;
 ptrDataPoint2-&gt;priceCurve[4]=20;
 ptrDataPoint2-&gt;priceCurve[5]=21;
 ptrDataPoint2-&gt;priceCurve[6]=17;
 ptrDataPoint2-&gt;priceCurve[4]=16;
 ptrDataPoint2-&gt;priceCurve[7]=14;
 ptrDataPoint2-&gt;index=7;

 printf("test17:mixed but major down trend direction:getPriceDirection() --&gt;\n");
 getPriceDirection(cptr);



 ptrDataPoint2-&gt;priceCurve[0]=25;
 ptrDataPoint2-&gt;priceCurve[1]=20;
 ptrDataPoint2-&gt;priceCurve[2]=18;
 ptrDataPoint2-&gt;priceCurve[3]=17;
 ptrDataPoint2-&gt;priceCurve[4]=16;
 ptrDataPoint2-&gt;priceCurve[5]=15;
 ptrDataPoint2-&gt;priceCurve[6]=14;
 ptrDataPoint2-&gt;priceCurve[4]=13;
 ptrDataPoint2-&gt;priceCurve[7]=12;

 printf("test17 :Pure down trend direction:getPriceDirection() --&gt; \n");
 getPriceDirection(cptr);


 ptrDataPoint2-&gt;priceCurve[0]=14;
 ptrDataPoint2-&gt;priceCurve[1]=16;
 ptrDataPoint2-&gt;priceCurve[2]=17;
 ptrDataPoint2-&gt;priceCurve[3]=18;
 ptrDataPoint2-&gt;priceCurve[4]=20;
 ptrDataPoint2-&gt;priceCurve[5]=21;
 ptrDataPoint2-&gt;priceCurve[6]=24;
 ptrDataPoint2-&gt;priceCurve[4]=28;
 ptrDataPoint2-&gt;priceCurve[7]=29;


 printf("test17 : Pure upTrend direction:getPriceDirection() --&gt; \n");
 getPriceDirection(cptr);


 ptrDataPoint2-&gt;priceCurve[0]=14;
 ptrDataPoint2-&gt;priceCurve[1]=16;
 ptrDataPoint2-&gt;priceCurve[2]=17;
 ptrDataPoint2-&gt;priceCurve[3]=18;
 ptrDataPoint2-&gt;priceCurve[4]=10;
 ptrDataPoint2-&gt;priceCurve[5]=14;
 ptrDataPoint2-&gt;priceCurve[6]=17;
 ptrDataPoint2-&gt;priceCurve[4]=21;
 ptrDataPoint2-&gt;priceCurve[7]=22;


 printf("test17 : mixed but major upTrend direction: getPriceDirection() --&gt; \n");
 getPriceDirection(cptr);


 ptrDataPoint2-&gt;priceCurve[0]=61.76;
 ptrDataPoint2-&gt;priceCurve[1]=61.76;
 ptrDataPoint2-&gt;priceCurve[2]=61.85;
 ptrDataPoint2-&gt;priceCurve[3]=61.84;
 ptrDataPoint2-&gt;priceCurve[4]=61.75;
 ptrDataPoint2-&gt;priceCurve[5]=61.80;
 ptrDataPoint2-&gt;priceCurve[6]=61.79;
 ptrDataPoint2-&gt;priceCurve[4]=61.76;
 ptrDataPoint2-&gt;priceCurve[7]=61.75;

 printf("test17 : mixed --&gt;getPriceDirection() \n");
 getPriceDirection(cptr);

 free(cptr);

 /*** test 18****/

 printf("test18 :%d \n",123423 % 1000000);
 printf("test18 :%d \n",123423 % 100000);
 printf("test18 :%d \n",123423 % 10000);
 printf("test18 :%d \n",123423 % 10000);

 /*** test 19****/

 char stet[]="152345-152623";
 char * ptr_stet;

 printf("test19 strlen stet %d \n",strlen(stet));
 ptr_stet = strtok(stet,"-");
 while(ptr_stet!=NULL) {
  printf("test19 time:%f \n",atof(ptr_stet));
  ptr_stet = strtok(NULL,"-");

 }

 /**Test 20 ****/
 time_t now;
 time(&amp;now);
 struct tm * timeNow = localtime(&amp;now);
 mktime(timeNow);


 int startTime = 120000;
 //int startTime = 155800;
 int endTime = 121000;
 int numSlices= 5;
 int unloadTime = 500;

 int currentTime =timeNow-&gt;tm_hour*10000+timeNow-&gt;tm_min*100+timeNow-&gt;tm_sec;
 timeNow-&gt;tm_sec += 900;

 mktime(timeNow);

 int currentTimePlus15Min =timeNow-&gt;tm_hour*10000+timeNow-&gt;tm_min*100+timeNow-&gt;tm_sec;
 int currentTimeAsInt = currentTime/10000*3600 + currentTime/100*60+ currentTime%100;
 int start = startTime/10000*3600 + startTime/100*60+ startTime%100;
 int end = endTime/10000*3600 + endTime/100*60 + endTime%100;
 int temp1 = endTime/10000*3600 + endTime/100%100*60 + endTime%100- unloadTime;
 int temp2 =temp1/3600*10000+(temp1/60)%60*100+temp1%60;

 int nextTimeToSendSlice = 121020+300;
 int nextTimeToSendSlice2 = nextTimeToSendSlice/10000*3600 + nextTimeToSendSlice/100*60+nextTimeToSendSlice%100;


 if (nextTimeToSendSlice%100&gt;=60){
  nextTimeToSendSlice+=100;
  nextTimeToSendSlice-=60;
  printf("1. test20: currentTime %d currentTimePlus15Min %d currentTimeAsInt %d start %d end %d end-start %d SlicesTimeApart %d temp1 %d temp2 %d nextTimetoSendSlice%d\n\n", currentTime,currentTimePlus15Min,currentTimeAsInt,start,end,(end-start),(end-start)/numSlices,temp1,temp2,nextTimeToSendSlice);

 }

 if (nextTimeToSendSlice%10000&gt;=6000){

  nextTimeToSendSlice+=10000;
  nextTimeToSendSlice-=6000;
  printf("2. test20: currentTime %d currentTimePlus15Min %d currentTimeAsInt %d start %d end %d end-start %d SlicesTimeApart %d temp1 %d temp2%d nextTimeToSendSlice %d\n\n", currentTime,currentTimePlus15Min,currentTimeAsInt,start,end,(end-start),(end-start)/numSlices,temp1,temp2,nextTimeToSendSlice);

 }

 printf("3. test20: currentTime %d currentTimePlus15Min %d currentTimeAsInt %d start %d end %d end-start %d SlicesTimeApart %d temp1 %d temp2%d nextTimeToSendSlice %d nextTimeToSendSlice2 %d \n\n", currentTime,currentTimePlus15Min,currentTimeAsInt,start,end,(end-start),(end-start)/numSlices,temp1,temp2,nextTimeToSendSlice,nextTimeToSendSlice2);

 /*************** test 21 ****************************/
 tm tmpTime= tm();

 tmpTime.tm_sec=57;
 tmpTime.tm_min=41;
 tmpTime.tm_hour=14;
 tmpTime.tm_mday=2;
 tmpTime.tm_mon= 1;
 tmpTime.tm_year=2006-1900;

 time_t myTimeT = mktime(&amp;tmpTime);


 tm tmpTime1= tm();
 tmpTime1.tm_sec=02;
 tmpTime1.tm_min=42;
 tmpTime1.tm_hour=14;
 tmpTime1.tm_mday=2;
 tmpTime1.tm_mon= 1;
 tmpTime1.tm_year=2006-1900;

 time_t myTimeT2 = mktime(&amp;tmpTime1);

 printf(" test21: the diff %f \n\n\n",difftime(myTimeT2,myTimeT));

 /********** test 22 ***********/

 int st1=150000;
 //int subtractsecs= 30;
 int subtractsecs= 30;

 int st2=154500;
 int st3=154607;

 //int st4=154558;
 //int addsecs =35;

 int st4=150000;
 //int addsecs =35;
 int addsecs =300;

 printf("test22:1 :st1 %d  subtractSeconds %d subtractSecondsfromTime %d \n",st1,subtractsecs,subtractSecondsFromTime(st1,subtractsecs));

 /****************** test 24 *************/

 time_t now123;
 time(&amp;now123);

 struct tm * timeNow123 = localtime(&amp;now123);
 mktime(timeNow123);

 int currentTime123 =timeNow123-&gt;tm_hour*10000+timeNow123-&gt;tm_min*100+timeNow123-&gt;tm_sec;
 int current123 = currentTime123/10000*3600 + currentTime123/100*60+currentTime123%100;

 printf("test 24 timeNow123-&gt;tm_hour %d,timeNow123-&gt;tm_hour*10000 %d,timeNow123-&gt;tm_min %d,timeNow123-&gt;tm_min*100 %d,timeNow123-&gt;tm_sec %d \n",

  timeNow123-&gt;tm_hour,timeNow123-&gt;tm_hour*10000,timeNow123-&gt;tm_min,timeNow123-&gt;tm_min*100,timeNow123-&gt;tm_sec);

 printf("test 24 currentTime123 %d \n",currentTime123);
 /*
 int startTime=
 int endTime =
 int end = endTime/10000*3600 + endTime/100*60+endTime%100;
 int start = startTime/10000*3600 + startTime/100*60+startTime%100;
 */

 /*********************test 25 ***************/
 /*
 char stet[]="152345-152623";
 char * ptr_stet;

 printf("test19 strlen stet %d \n",strlen(stet));
 ptr_stet = strtok(stet,"-");
 while(ptr_stet!=NULL) {
 printf("test19 time:%f \n",atof(ptr_stet));
 ptr_stet = strtok(NULL,"-");

 }  

 */

 char  strfoo[] ="34.58-10000-34.89-250000-36.78-24000";
 char * ptr_strfoo=NULL;

 ptr_strfoo = strtok(strfoo,"-");


 //printf("test25  1:%f \n",atof(ptr_strfoo));
 //printf("test 25 strlen ptr_strfoo %d\n",strlen(ptr_strfoo));

 while(ptr_strfoo != NULL) {
  /*
  char pxvol[strlen(ptr_strfoo)];
  strcpy(pxvol,ptr_strfoo);
  char * ptr_pxvol=NULL;
  ptr_pxvol= strtok(pxvol,":");

  while(ptr_pxvol != NULL) {
  printf("test25 -----b Value--&gt;%f \n",atof(ptr_pxvol));
  ptr_pxvol= strtok(NULL,":");
  }
  */
  /* Below work
  printf("test25  Price:%s \n",ptr_strfoo);
  ptr_strfoo = strtok(NULL,"-");
  printf("test25  Qty :%s \n",ptr_strfoo);
  ptr_strfoo = strtok(NULL,"-");
  */

  printf("test25  Price:%f \n",atof(ptr_strfoo));
  ptr_strfoo = strtok(NULL,"-");
  printf("test25  Qty :%f \n",atof(ptr_strfoo));
  ptr_strfoo = strtok(NULL,"-");
 }   


 /*********** test 26 **********/
 char * fooarr[5];
 fooarr[0]= (char*)malloc(100);
 strcpy(fooarr[0],"1 This is test26.Array of char ptrs");

 fooarr[1]= (char*)malloc(100);
 strcpy(fooarr[1],"2 test 26 .Box car racers ar here");

 fooarr[2]= (char*)malloc(100);
 strcpy(fooarr[2],"3 test 26 .Box car racers ar here");

 fooarr[3]= (char*)malloc(100);
 strcpy(fooarr[3],"4. test 26 .Box car racers ar here");

 fooarr[4]= (char*)malloc(100);
 strcpy(fooarr[4],"5. test 26 .Box car racers ar here");

 fooarr[5]= (char*)malloc(100);
 strcpy(fooarr[5],"6. test 26 .Box car racers ar here");
/* Need to malloc more space or else below will run out of space
 for (int x =0; x&lt; 5;x++) {

  printf("test26  fooarr x %d %s\n ",x,fooarr[x]);
 }
 */

 free(*fooarr);

 /*********** test 27 **********/

 /*** test 27****/

 char symstr[]="ABC.O";
 char * place;

 place = strtok(symstr,".");

 printf("test27 1 time:%s \n",place);
 place = strtok(NULL,".");
 printf("test27 2 time:%s \n",place);

 /*********** test 28 **********/


 double price1 = 28.75;
 double price2 = 28.7589;      
 double price3 = 28.7524;      
 double price4 = 28.7; 
 printf("test28 price %f  roundPrice %f\n",price1,roundPrice("buy",price1));
 printf("test28 price %f  roundPrice %f\n",price2,roundPrice("buy",price2));
 printf("test28 price %f  roundPrice %f\n",price3,roundPrice("buy",price3));
 printf("test28 price %f  roundPrice %f\n",price4,roundPrice("buy",price4));

 /*********** test 29 **********/

 string sfoo = "5000@20=100000";
 getSmartExpStr(sfoo);

 /*********** test 30 **********/

 //              string sf=getExpStr(500,20.2);


 //              printf("getExpStr %s\n",sf.c_str());
 string sf= ToString<int>(42); 
 printf("test 30 sf %s\n",sf.c_str());

 string p1977=getExpStr(1000000,180.98);
 printf("test 30 getExpStr %s\n",p1977.c_str());

 p1977=getExpStr(500,20.2);
 printf("test 30 getExpStr %s\n",p1977.c_str());

 /*********** test 31 **********/

 printf ("test 31 percentd %d \n",25/2);
 printf ("test 31 percentf %f \n",25/2);
 printf ("test 31 percentfABS %f \n",abs(25/2));

 /*********** test 32 **********/

 double * ptr_double = testPtrReturn();
 printf("test 32 ptr_double :testPtrReturn() %f\n", *ptr_double);

 double  ptr_double2 = *testPtrReturn();
 printf("test 32 ptr_double2 :testPtrReturn()  %f\n", ptr_double2);


 /*********** test 34 **********/

 //printf("testing 34 ref return  %s\n", testStrRefRtrn());

 /*********** test 35 **********/

 A aobj;
 A* aptr = new A();
 A&amp; aref=(*aptr);
 delete (aptr);

 /*********** test 35 **********/
 //FOO foo foo

 class Ins {
 public:
  virtual void foo() {
   printf("Test 35 : class Ins: foo \n");
  }
  virtual ~Ins(){}

 };

 class fx : public Ins {
 public:
  virtual void foo() {
   printf("Test 35 : class fx: foo \n");
  }

  virtual ~fx(){}
 };

 Ins *iptr = new Ins();
 fx * fptr = new fx();
 iptr=&amp;(*fptr);

 iptr-&gt;foo();
 delete (iptr); // fptr will be gone as wellss
 //delete (fptr);


 /***** test 37************/


 MZSingleton *myptrz = MZSingleton::getMZSingleton1();
 MZSingleton *myptrz2 = myptrz-&gt;getMZSingleton1();
 //                        MZSingleton &amp; myrefz =MZSingleton::getMZSingleton1();
 // Singleton *p2 = p1-&gt;Instance();
 // Singleton &amp; ref = * Singleton::Instance();
 
/*** test 38  create thread argument struct for thr_func() ***/

  pthread_t thr[NUM_THREADS];
  int i, rc;
  /* create a thread_data_t argument array */
  thread_data_t thr_data[NUM_THREADS];
 
  /* create threads */
  for (i = 0; i &lt; NUM_THREADS; ++i) {
    thr_data[i].tid = i;
    if ((rc = pthread_create(&amp;thr[i], NULL, thr_func, &amp;thr_data[i]))) {
      fprintf(stderr, "Test38:test38: error: pthread_create, rc: %d\n", rc);
      return EXIT_FAILURE;
    }
  }
  /* block until all threads complete */
  for (i = 0; i &lt; NUM_THREADS; ++i) {
    pthread_join(thr[i], NULL);
  }
 
 return 0;
}


</int></char></40></1></0></0></0></10></char></class></double></double></int></class></pthread></cstdlib></string></ctime></time></math></sstream></string></stdio></pre>
</body>
</html></div>


