# 2pr

1)Таблица OE.CUSTOMERS. Сформируйте новое поле GENDER_GROUP,

    select 
      case lower(GENDER)
        when 'm' 
            then 1
        else 0
    end as GENDER_GROUP
    from oe.customers


2)Таблица OE.orders, oe.customers, oe.order_items 

    select 
        CUSTOMER_ID,
    sum(order_total) as sum_of_price
    from oe.orders
    group by customer_id;


а)Построить запрос, который выводит id клиента, цену товара, кол-во товара 


    select 
        customer_id,
        sum(total_price) as total_price,
        sum(total_quantity) as total_quantity
    from (
        select 
            t1.customer_id,
            t1.total_price,
            t2.total_quantity
        from (
       
            select
                customer_id,
                order_id,
               sum(order_total) as total_price
            from oe.orders
            group by customer_id, order_id) t1
        join (
        
           select 
               order_id,
               sum(quantity) as total_quantity
           from oe.order_items
           group by order_id) t2
        on t1.order_id = t2.order_id)
    group by customer_id;



б)Посчитать кол-во денег на id товара 

    select
       product_id
       sum(unit_price) * sum(quantity) as total_price
    from oe.order_items
    group by product_id;

в)Подтянуть по id имя и фамилию клиента

    select
        t1.*,
        t2.cust_first_name,
        t2.cust_last_name
    from oe.orders t1
    join oe.customers t2
    on t1.customer_id = t2.customer_id;

г)Посчитать сколько в общем клиент потратил денег

    select
        customer_id,
        sum(ORDER_TOTAL)
    from oe.orders
    group by customer_id;



3)Таблица OE.CUSTOMERS OE.orders hr.employees


    select
       t1.cust_first_name,
        t3.first_name
    from oe.customers t1
    join oe.orders t2
    on t1.customer_id = t2.customer_id
    join hr.employees t3
    on t2.sales_rep_id = t3.employee_id;



    select 
      t1.*
    from oe.orders t1
    join (
        select 
           customer_id,
           sales_rep_id,
           count_of_buy
        from (
            select
               customer_id,
                sales_rep_id,
                row_number() over (partition by concat(customer_id, concat(' ', sales_rep_id)) 
                                   order by sales_rep_id) as count_of_buy
            from oe.orders
            where sales_rep_id in (select
                                       sales_rep_id
                                   from oe.orders
                                   group by sales_rep_id)) 
       where count_of_buy = 2) t2
    on t1.customer_id = t2.customer_id and t2.sales_rep_id = t1.sales_rep_id;


4)Таблица  OLYM.OLYM_MEDALS и OLYM.OLYM_GAMES



    select
       t1.city,
       count(t2.medal)
    from olym.olym_games t1
    join olym.olym_medals t2
    on t1.id = t2.athlete_game_id
    and t1.year = 2000
    group by t1.city;
