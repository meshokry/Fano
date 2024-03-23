#include <stdio.h>
#include <stdlib.h>

typedef struct element element;

struct element{

float probability;
int data;
char symbol;

};

/*function to reorder the array*/
void reorder_the_array(element**arr,int number_of_inputs)
{
    int counter1,counter2,container1,container2;
    element temp;

    for(counter1=0;counter1<number_of_inputs-1;counter1++)
    {
       for(counter2=counter1+1;counter2<number_of_inputs;counter2++)
       {
           //using containers to avoid comparing between two float values
           container1=(int)(((*arr)[counter2].probability)*10000);
           container2=(int)(((*arr)[counter1].probability)*10000);
           if(container1>container2)
           {
               //swapping
               temp=(*arr)[counter2];
              (*arr)[counter2]=(*arr)[counter1];
              (*arr)[counter1]=temp;
           }
       }
    }
}

/*function to get the summation of the array elements*/
float get_summation(element*arr,int start,int end)
{
    int row;
    float sum=0;
    for(row=start;row<=end;row++)
    {
        sum=arr[row].probability+sum;
    }
    return sum;
}

/*function to print array*/
void print_array(element*arr,int number_of_inputs)
{
    int row;
    for(row=0;row<number_of_inputs;row++)
    {
        printf("%c = %f  %d\n",arr[row].symbol,arr[row].probability,arr[row].data);
    }

}

element get_index_of_sepration(element *arr,float target,int start,int end)
{
    float upper_sum=0,lower_sum=0;
    int row,upper_index,lower_index;
    element threshold;

    for(row=start;upper_sum<target;row++)
    {
        upper_sum=arr[row].probability+upper_sum;
        upper_index=row;
    }
    for(row=end;lower_sum<target;row--)
    {
        lower_sum=arr[row].probability+lower_sum;
        lower_index=row;
    }

    if((lower_sum-target)<=(upper_sum-target))
    {
       threshold.data=lower_index;
       threshold.symbol='L';
    }
    else if((lower_sum-target)>(upper_sum-target))
    {
        threshold.data=upper_index;
        threshold.symbol='U';
    }
    else
    {
        threshold.data=999;
        threshold.symbol='E';
        printf("Error in get index function");
    }
    return threshold;

}
/*function for generating Fanno code*/
void fanno_code(element*arr,int start,int end)
{
    element split_index;
    int counter;
    float sum;
    if(end<0)
    {
        end=0;
    }
    if (start == end) {
        return;
    }

    sum=get_summation(arr,start,end);

    split_index=get_index_of_sepration(arr,sum/2,start,end);

    if(split_index.symbol=='L')
    {
        for(counter=start;counter<split_index.data;counter++)
        {
            assign_zero(arr,counter);
        }
        for(counter=end;counter>=split_index.data;counter--)
        {
            assign_one(arr,counter);
        }

        fanno_code(arr, start, split_index.data - 1);
        fanno_code(arr, split_index.data, end);
    }
    else if(split_index.symbol=='U')
    {
        for(counter=start;counter<=split_index.data;counter++)
        {
            assign_zero(arr,counter);
        }
        for(counter=end;counter>split_index.data;counter--)
        {
            assign_one(arr,counter);
        }

        fanno_code(arr, start, split_index.data);
        fanno_code(arr, split_index.data+1, end);

    }
    else
    {
        printf("ERROR in Fanno function");
    }


}
/*function to assign zero*/
void assign_zero(element*arr,int row)
{
    arr[row].data=arr[row].data*10+2;/*adding 2 to represent zero*/
}
/*function to assign one*/
void assign_one(element*arr,int row)
{
     arr[row].data=arr[row].data*10+1;/*adding 1 to represent one*/
}
/*function to get ten^number that it takes*/
int get_tens(int number)
{
    int counter=1;

    while(number/10!=0)
    {
        number/=10;
        counter=counter*10;
    }
    return counter;
}

int main()
{
    int number_of_inputs,row,start=0,end;
    element index;

    //Taking number of inputs from user
    printf("Enters number of values\n");
    scanf("%d",&number_of_inputs);
    end=number_of_inputs-1;
    //creating array
    element*arr=(element*)malloc((number_of_inputs)*sizeof(element));

    //Getting symbols & probabilities from user
   for(row=0;row<number_of_inputs;row++)
    {
        printf("Enter symbol %d\n",row+1);
        scanf(" %c",&arr[row].symbol);
        printf("Enter probability %d\n",row+1);
        scanf("%f",&arr[row].probability);
        arr[row].data=0;
    }
//rearrange array
    reorder_the_array(&arr,number_of_inputs);

    print_array(arr,number_of_inputs);

    fanno_code(arr,start,end);
/*********************************************************************************/
                 /*printing code*/

int digit,tens;

printf("\nSymbol\t\tProbability\t\tCode\n");

for(row=0;row<number_of_inputs;row++)
{
    tens = get_tens(arr[row].data);


    printf("%c\t\t%f\t\t",arr[row].symbol,arr[row].probability);

    while(tens!=0)
    {
        digit=(arr[row].data)/tens;/*getting digit through dividing on ten multiplied by number of digits minus one*/

        (digit>1)?printf("0"):printf("1");/*if the digit equal 2 print 0 and if digit equals 1 print 1*/

        (arr[row].data)=(arr[row].data)-digit*tens;/*deleting digit after checking it*/

        tens/=10;   /*reducing it by digit*/
    }
    printf("\n");
}


system("pause");


    return 0;
}
