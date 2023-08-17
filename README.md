# automate parallel processing :
data engineering project runs airflow on AWS EC2 to applay parall tasks  on AWS RDS Postgres instance database as the database .
all commands used :
sudo apt update
sudo apt install python3-pip
sudo apt install python3.10-venv
python3 -m venv airflow_venv
sudo pip install pandas 
sudo pip install s3fs
sudo pip install fsspec
sudo pip install apache-airflow
sudo pip install apache-airflow-providers-postgres
psql -h rds-db-test-yml-4.cvzpgj7bczqy.us-west-2.rds.amazonaws.com -p 5432 -U postgres -W
CREATE EXTENSION aws_s3 CASCADE;

aws iam create-role \
    --role-name postgresql-S3-Role-yml-4 \
    --assume-role-policy-document '{"Version": "2012-10-17", "Statement": [{"Effect": "Allow", "Principal": {"Service": "rds.amazonaws.com"}, "Action": "sts:AssumeRole"}]}'

aws iam create-policy \
    --policy-name postgresS3Policy-yml-4 \
    --policy-document '{"Version": "2012-10-17", "Statement": [{"Sid": "s3import", "Action": ["s3:GetObject", "s3:ListBucket"], "Effect": "Allow", "Resource": ["arn:aws:s3:::testing-ymlo", "arn:aws:s3:::testing-ymlo/*"]}]}'

aws iam attach-role-policy \
    --policy-arn arn:aws:iam::177571188737:policy/postgresS3Policy-yml-4 \
    --role-name postgresql-S3-Role-yml-4

aws rds add-role-to-db-instance \
   --db-instance-identifier rds-db-test-yml-4 \
   --feature-name s3Import \
   --role-arn arn:aws:iam::177571188737:role/postgresql-S3-Role-yml-4   \
   --region us-west-2

