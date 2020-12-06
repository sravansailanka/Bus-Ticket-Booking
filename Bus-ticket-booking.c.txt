#include<stdio.h>
#include<stdlib.h>
#include<string.h>
struct bus_details
{
    char* from_to;
    int bus_no,fare;

}b[3];
struct passengers_details
{
    int age;
    char name[20];
}pass[6];
struct previous_booking
{
  int bus_num;
  char from_to[8];
  int tickets,amount;
  char contact_num[20];
}f;
void display_bus_details()
{
    int i;
    b[0].from_to="HYD-VIJ";
    b[1].from_to="HYD-VIZ";
    b[2].from_to="HYD-RJM";

    b[0].bus_no=1432;
    b[1].bus_no=2341;
    b[2].bus_no=8854;

    b[0].fare=450;
    b[1].fare=1000;
    b[2].fare=750;

    printf("\nBUS DETAILS :\n");
    printf("\t\tFrom-to\t\tBus no\t\tFare(per ticket)\n\n");
    for(i=0;i<3;i++)
    {
        printf("\t\t%s\t\t%d\t\t%d\n",b[i].from_to,b[i].bus_no,b[i].fare);
    }
    printf("\t\tHYD-Hyderabad\nVIJ-Vijayawada\nVIZ-Vizag\nRJM-Rajamundry\n");
}
void scan_tickets(int s[],int seat_no[],int *p)
{
    int i,j,count;
    printf("enter no of tickets:");
    scanf("%d",p);
    while(*p>6 ||*p<=0 )
    {
        while(*p<=0)
        {
            printf("No of tickets at least should be %d\nenter number of tickets:",1);
            scanf("%d",p);

        }
        if(*p>6)
        {
            printf("max tickets can be booked at a time are %d\nenter number of tickets:",6);
            scanf("%d",p);
        }
    }

    printf("enter seat numbers\n");
    i=0;
    while(i<(*p))
    {
        scanf("%d",&seat_no[i]);
        if(seat_no[i]>40 || seat_no[i]<1)
        {
            printf("OOPS..! You have entered a wrong seat number\n");
            printf("Re-enter the seat number:");
            continue;
        }
        if(s[seat_no[i]-1]==64)
        {
            printf("seat is occupied");
            printf("\nEnter another seat:" );
            continue;
        }
        count=0;
        for(j=0;j<i;j++)
            if(seat_no[i]==seat_no[j])
                count++;
        if(count==0)
        {
            i++;
        }
        else
        {
            printf("%d seat number is repeated time(s) previously\n",seat_no[i]);
            printf("re enter the seat number:");
        }
    }
}

void current_seats(int s[])
{
    int i;
    printf("\t\t");
    for(i=0;i<40;i++)
    {
        if(i%8==0 || i%8==1 || i%8==2 || i%8==3)
        {
           if(s[i]==42 || s[i]==64)
                printf("%c  ",s[i]);
            else
                printf("%d ",s[i]);
        }
        else
        {
            if(i%8==4)
            {
                if(s[i+3]==42 || s[i+3]==64)
                    printf("%c  ",s[i+3]);
                else
                    printf("%d ",s[i+3]);
            }
            else if(i%8==5)
            {
                if(s[i+1]==42 || s[i+1]==64)
                    printf("%c  ",s[i+1]);
                else
                    printf("%d ",s[i+1]);
            }
            else if(i%8==6)
            {
                if(s[i-1]==42 || s[i-1]==64)
                    printf("%c  ",s[i-1]);
                else
                    printf("%d ",s[i-1]);
            }
            else
            {
                if(s[i-3]==42 || s[i-3]==64)
                    printf("%c  ",s[i-3]);
                else
                    printf("%d ",s[i-3]);
            }
        }
        if((i+1)%2==0)
            printf("\t\t");
        if((i+1)%4==0)
            printf("\n\t\t");
    }
    printf("\b\b\b\b\b\b\b\b\b\b\b\b\b\b\b\b'@'-occupied\n'*'-selected \n");
}
void Bus(int s[],int seat_no[],int *p)
{
    int i;
    scan_tickets(s,seat_no,p);
    for(i=0;i<(*p);i++)
        s[seat_no[i]-1]=42;
    current_seats(s);
    for(i=0;i<(*p);i++)
        s[seat_no[i]-1]=64;
}
void pass_details(struct passengers_details pa[],int *p)
{
    int i;
    printf("\n\tPassengers details");
    for(i=1;i<=(*p);i++)
    {
        printf("\npassenger %d:",i);
        printf("\n\tname:");
        fflush(stdin);
        gets(pa[i-1].name);
        printf("\tage :");
        fflush(stdin);
        scanf("%d",&pa[i-1].age);
        printf("\n");
    }
}
void bill(struct previous_booking m,struct bus_details s[],struct passengers_details p[],int seat_no[],char num[],int seats,int busno)
{

    FILE *fp;
    int i,x;
    printf("\n\n******************BILL*****************\n");
    printf("BUS NO:%d\n",busno);
    printf("FROM - Hyderabad\t");
    if(busno==s[0].bus_no)
    {
        printf("TO - Vijayawada\n");
        x=0;
    }
    else if(busno==s[1].bus_no)
    {
        printf("TO - Vizag\n");
        x=1;
    }
    else
    {
        printf("TO - Rajamundry\n");
        x=2;
    }
    f.bus_num=busno;
    strcpy(f.from_to,b[x].from_to);
    f.tickets=seats;
    f.amount=seats*(s[x].fare);
    strcpy(f.contact_num,num);
    fp=fopen("previous_booking.dat","a");
    fwrite(&f,sizeof(f),1,fp);
    fclose(fp);

    printf("PASSENGERS DETAILS\n");
    printf("name(age)  -  seat number\n");
    for(i=0;i<seats;i++)
        printf("%s(%d) - %d\n",p[i].name,p[i].age,seat_no[i]);
    printf("CONTACT NUMBER   - ");
    printf("%s\n",num);
    printf("NUMBERS OF SEATS - %d\n",seats);
    printf("TOTAL AMOUNT     - %d\n",seats*(s[x].fare));
    printf("****************************************\n\n");

}
void display_previous_booking(struct previous_booking f,int no_of_bookings)
{
    FILE *fp;
    int i;
    fp=fopen("previous_booking.dat","a+");
    printf("BUS_NO\t\tFROM_TO\t\tNO_OF_TICKETS\t\tTOTAL_AMOUNT\t\tCONTACT_NUM\n");
    for(i=0;i<no_of_bookings;i++)
    {
        fread(&f,sizeof(f),1,fp);
        printf("%d  \t\t%s\t\t%d\t\t\t%d\t\t\t %s \n",f.bus_num,f.from_to,f.tickets,f.amount,f.contact_num);
    }
    fclose(fp);

}
int main()
{
    int A[40],B[40],C[40],bus_number,count=0,choice;
    char contactnum[20];
    int seat_no[6],no_of_tickets,i,no_of_bookings=1;
    FILE *fp,*fp1;

    printf("                WELCOME TO SS TRAVELS BOOKING\n");
    display_bus_details();

    printf("\n\t\tenter bus number:");
    while(1)
    {
        scanf("%d",&bus_number);
        for(i=0;i<3;i++)
            if(bus_number==b[i].bus_no)
                count++;
        if(count==1)
            break;
        else
            printf("\t\tenter valid bus number:");
    }
    for(i=0;i<40;i++)
    {
        A[i]=i+1;
        B[i]=i+1;
        C[i]=i+1;
    }
    while(1)
    {
        printf("\t\tFROM - Hyderabad\t");
        if(bus_number==b[0].bus_no)
        {
            printf("TO - Vijayawada\n");
            printf("current seat availability\n");
            current_seats(A);
            Bus(A,seat_no,&no_of_tickets);
        }
        else if(bus_number==b[1].bus_no)
        {
            printf("TO - Vizag\n");
            printf("current seat availability\n");
            current_seats(B);
            Bus(B,seat_no,&no_of_tickets);
            }
        else
        {
            printf("TO - Rajamundry\n");
            printf("current seat availability\n");
            current_seats(C);
            Bus(C,seat_no,&no_of_tickets);
        }
    pass_details(pass,&no_of_tickets);

    while(1)
    {
        printf("enter contact number:");
        scanf("%s",contactnum);
        if(strlen(contactnum)==10)
            break;
        else
            printf("enter a 10-Digit contact number\n");
    }
    bill(f,b,pass,seat_no,contactnum,no_of_tickets,bus_number);
        while(1)
        {
            printf("\n\n1.Display bus details\n");
            printf("2.Check seat availability\n");
            printf("3.Book again\n");
            printf("4.Display previous booking\n");
            printf("enter any character to exit booking\n");
            printf("Enter any option number:");
            scanf("%d",&choice);
            if(choice==1)
            {
                display_bus_details();
            }

            else if(choice==2)
            {
                printf("enter bus number:");
                while(1)
                {
                    count=0;
                    scanf("%d",&bus_number);
                    for(i=0;i<3;i++)
                        if(bus_number==b[i].bus_no)
                            count++;
                    if(count==1)
                        break;
                    else
                        printf("enter valid bus number:");
                }
                if(bus_number==b[0].bus_no)
                    current_seats(A);
                else if(bus_number==b[1].bus_no)
                    current_seats(B);
                else
                    current_seats(C);
            }
            else if(choice==3)
            {
                count=0;
                printf("enter bus number:");
                while(1)
                {
                    scanf("%d",&bus_number);
                    for(i=0;i<3;i++)
                        if(bus_number==b[i].bus_no)
                            count++;
                    if(count==1)
                        break;
                    else
                        printf("enter valid bus number:");
                }
                no_of_bookings++;
                break;
            }
            else if(choice==4)
            {
                display_previous_booking(f,no_of_bookings);
            }
            else
            {
                char ch;
                fp=fopen("previous_booking.dat","r");
                fp1=fopen("previous_records.dat","a");
                while((ch=fgetc(fp))!=EOF)
                {
                    fputc(ch,fp1);
                }
                fclose(fp);
                fp=fopen("previous_booking.dat","w");
                fclose(fp);
                fclose(fp1);
                printf("\n\n<<<<<<<<<<<<<THANK YOU FOR BOOOKING>>>>>>>>>>>>>>>>\n\n");
                return 0;
            }
        }
    }
}

