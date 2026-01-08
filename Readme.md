+---------------------------+
                         | Internet |
                         +-------------+-------------+
                                       |
                                       | (public traffic)
                                       v
+---------------------------------------------------------------+
| Route 53 (DNS) |
| |
| aws_route53_zone.domain (Hosted Zone: var.domain_name) |
| | |
| +--> aws_route53_record.a_record_item (A → IP) |
| +--> aws_route53_record.cname_record_item (CNAME → name) |
+-------------------------------+-------------------------------+
                                |
                                | DNS gives IP / name mapping
                                v

+-----------------------------------------------------------------------+
| AWS VPC: aws_vpc.vpc (CIDR: var.cidr_block_range) |
| enable_dns_support=true, enable_dns_hostnames=true |
| |
| +-------------------+ |
| | IGW | |
| | aws_internet_gateway.igw |
| +---------+---------+ |
| | |
| | Route: 0.0.0.0/0 → IGW |
| v |
| +-----------------------------+ |
| | Public Route Table | |
| | aws_route_table.rtb_public | |
| | - 0.0.0.0/0 → igw | |
| +--------------+--------------+ |
| | Association |
| | aws_route_table_association.rta_subnet_public |
| v |
| +--------------------------------------------------+ |
| | Public Subnet | |
| | aws_subnet.subnet_public | |
| | CIDR: var.subnet1_cidr_block_range | |
| | AZ: var.availability_zone | |
| | map_public_ip_on_launch = true | |
| | | |
| | (EC2 would be launched here using aws_key_pair.ec2key) |
| +--------------------------------------------------+ |
| |
| Key Pair (for EC2 login) |
| aws_key_pair.ec2key (key_name="publicKey") |
+-----------------------------------------------------------------------+
