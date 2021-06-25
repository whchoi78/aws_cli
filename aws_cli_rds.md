DB 서브넷 그룹 생성
aws rds create-db-subnet-group --db-subnet-group-description "infosec-db3" --db-subnet-group-name infosec-db-subnet3 --subnet-ids "subnet-0d0cf5d6b6e5cdf54" "subnet-03e6d98bfb72ad240" 
