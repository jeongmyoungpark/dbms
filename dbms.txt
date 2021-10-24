#include "db.h"

DB* head = NULL; // 두 번째 노드 다음은 노드가 없음(NULL)



int main(int argc, char* argv[]) {

	//argv[0] 실행경로

	

	log_in(argv[1], argv[2]); //로그인

	while (1)

	{

		char text1[100] = { '\0', };

		gets(text1);

		char* ptr = strtok(text1, ";");

		//printf("입력 문자 : %s\n", ptr);

		parse_str(ptr);

	}

	return 0;

}



void log_in(char* name, char* pwd) {



	if (strcmp(name, "root") == 0 && strcmp(pwd, "1234") == 0) {

		printf("LOG IN success\n");

	}

	else {

		printf("log in fail\n");

		exit(1);

	}

}



void parse_str(char* text) {

	char* ptr = strtok(text, " ");



	while (ptr != NULL)

	{



		if (strcmp(ptr, "CREATE") == 0) //DB생성,TABLE생성

		{

			ptr = strtok(NULL, " ");



			if (strcmp(ptr, "DATABASE") == 0)

			{

				ptr = strtok(NULL, "");

				create_db(ptr);

				//printf("head : %p\n", head);

			}

			else if (strcmp(ptr, "TABLE") == 0)

			{

				ptr = strtok(NULL, "");//table name

				create_tb(ptr);

			}



		}

		else if (strcmp(ptr, "DROP") == 0) {

			//printf("drop\n");

			ptr = strtok(NULL, " ");//두번째 문자



			if (strcmp(ptr, "DATABASE") == 0)

			{

				printf("drop db sucess\n");

				ptr = strtok(NULL, " ");//두번째 문자

				drop_db(ptr);



			}

			else if (strcmp(ptr, "TABLE") == 0)

			{

				//printf("table\n");

				ptr = strtok(NULL, " ");//두번째 문자

				drop_tb(ptr);

			}

		}

		else if (strcmp(ptr, "USE") == 0) {

			ptr = strtok(NULL, "");//두번째 문자

			//printf(ptr);

			use_db(ptr);



		}

		else if (strcmp(ptr, "SHOW") == 0) {//DB확인, TABLE 확인

			//printf("show\n");

			ptr = strtok(NULL, " ");



			if (strcmp(ptr, "DATABASES") == 0)

			{

				printf("database\n");

				show_db(head);

				//printf("head : %p\n", head);



			}

			else if (strcmp(ptr, "TABLES") == 0)

			{



				printf("table\n");

				show_tb();

			}

		}

		else if (strcmp(ptr, "DESC") == 0) {

			ptr = strtok(NULL, " ");

			printf("%s\n", ptr);//테이블명

			desc_tb(ptr);



		}

		else if (strcmp(ptr, "INSERT") == 0) {//INSERT into TABLE VALUES()

			ptr = strtok(NULL, " ");

			if (strcmp(ptr, "into") == 0) {

				ptr = strtok(NULL, "");



				insert_data(ptr);

				

				

			}



		}

		else if (strcmp(ptr, "SELECT") == 0) {

			ptr = strtok(NULL, "");



			char temp_else[100] = { '\0', };//*이외의 것일때

			strcpy(temp_else, ptr);



			char* next = NULL;//포인터 두개로 별일때 자르기

			next = strtok(temp_else, "");

			select_data(next);





		}

		else if (strcmp(ptr, "UPDATE") == 0) {

			ptr = strtok(NULL, "");

			update_data(ptr);

		}

		else if (strcmp(ptr, "DELETE") == 0) {

			//printf("delete\n");

			ptr = strtok(NULL, " ");

			//printf("%s\n",ptr); //from

			ptr = strtok(NULL, "");

			//printf("%s", ptr);



			delete_table(ptr);

		}

		ptr = strtok(NULL, " ");

	}

}



void create_db(char* new_db) {

	printf("create %s\n", new_db);

	DB* cur_db = head;

	while (cur_db != NULL)               // 포인터가 NULL이 아닐 때 계속 반복

	{

		if (strcmp(cur_db->db_name, new_db) == 0) {

			printf("db name can't use.");

			return;

		}

		cur_db = cur_db->d_next;             // 포인터에 다음 노드의 주소 저장

	}



	DB* db1 = (DB*)malloc(sizeof(DB));

	strcpy(db1->db_name, new_db);// 삽입

	db1->d_next = NULL;

	db1->table = NULL;



	if (head == NULL) {

		head = db1;

	}

	else

	{

		DB* cur = head;

		while (cur->d_next != NULL) {

			cur = cur->d_next;

		}

		cur->d_next = db1;

	}

	

	printf("***********************\n");

}

void drop_db(char* dbname) {

	//printf("%s", dbname);

	DB* cur_db = head;

	DB* pre_cur_db = head;



	while (cur_db != NULL) {



		if (strcmp(cur_db->db_name, dbname) == 0) { // 이름이 같냐



			//처음일 때

			if (cur_db == head) { // 첫번째 tb

				head = cur_db->d_next;

				delete_db_all(cur_db);

				free(cur_db);

			}

			else {//처음 이외 (중간, 마지막 포함)

				pre_cur_db->d_next = cur_db->d_next;

				delete_db_all(cur_db);

				free(cur_db);

			}



			break;



		}



		pre_cur_db = cur_db;

		cur_db = cur_db->d_next;



	}

	//

	printf("*************************\n");

}

void use_db(char* db_name) {

	DB* cur_db = head;

	while (1)

	{

		if (strcmp(db_name, cur_db->db_name) == 0)

		{

			db_cur = cur_db;

			printf("sucess use %s\n", db_name);

			break;

		}

		cur_db = cur_db->d_next;

		//다음 db

	}

	printf("***********************\n");

}

void create_tb(char* new_tb) {

	char* ptr = strtok(new_tb, "(");

	printf("create %s ", ptr);

	char* table_name;



	TABLE* tb1 = malloc(sizeof(TABLE));

	strcpy(tb1->tb_name, ptr);// 삽입

	tb1->t_next = NULL;

	tb1->column = NULL;

	if (db_cur->table == NULL) {

		db_cur->table = tb1;

		tb_cur = db_cur->table;

	}

	else

	{

		TABLE* cur = db_cur->table;

		while (cur->t_next != NULL)

		{

			cur = cur->t_next;

		}

		cur->t_next = tb1;

		tb_cur = cur->t_next;

	}

	//ptr = strtok(NULL, " ");





	ptr = strtok(NULL, ",");

	char* next = NULL;



	while (ptr != NULL) {

		//printf("현재 인자 : %s\n", ptr);

		next = strtok(NULL, "");

		//printf("next 인자 : %s\n", next);

		char temp[100] = { '\0', };

		

		strcpy(temp, ptr);

		

		if (!create_col(temp)) {



			//printf("123");

			drop_tb(tb1->tb_name);

			//printf("cursor : %s\n", db_cur->db_name);

			break;

		}

		ptr = strtok(next, ",)");

	}

	printf("\n");

	printf("***********************\n");



}

void drop_tb(char* tbname) {

	

	TABLE* cur_tb = db_cur->table; //db_cur은 전역으로 현재 사용중인 db가르킨다.

	TABLE* pre_cur_tb = db_cur->table;

	//tb_cur= cur_tb;



	if (db_cur->table == NULL) {

		printf("tb is not exist");

		printf("\n");

		printf("***********************\n");

		return;

	}

	/*

	if (strcmp(cur_tb->t_next->tb_name, tbname) == 0) {

		pre_cur_tb = cur_tb;

	}*/



	int find_flag = 0;



	//while (1) { // 처음 tb부터 마지막 tb까지 

	while(cur_tb != NULL){

		/*

		//헤드db일때 drop 1.하나만있을때 2.뒤에 연결된게 있을때

		

		// 처음일 때 X 

		// 현재 tb 가 가르키는 이름이 지우려고한 이름이랑 같냐

		if (strcmp(cur_tb->tb_name, tbname) == 0) { 

			db_cur->table = cur_tb->t_next; //데이터베이스가 가르키는 tb = 두번때 tb

			delete_tb_all(cur_tb);	// 첫번째 tb 데이터 전부 비우기

			free(cur_tb); //첫번째 tb free

			db_cur->table = NULL; // 첫번째 테이블을 null이다.

			break;

		}

		else if (cur_tb->t_next == NULL) { // 마지막일 때

			pre_cur_tb->t_next = NULL;	// 마지막에서 두번째 tb 가르키는 next 없다.

			delete_tb_all(cur_tb); // 마지막 tb 비우기

			free(cur_tb);	// 마지막 tb free

			//db_cur->table = NULL; // 첫번째 테이블은 없다.

			break;

		}

		else { // 처음도 아니고 마지막도 아닐때 // 중간일때

			// 이전 테이블이 가르키는 tb는 현재 가르키는 tb의 다음이다.

			pre_cur_tb->t_next = cur_tb->t_next;	

			delete_tb_all(cur_tb); // 현재 tb 전부 비우기

			free(cur_tb); // 현재 tb free

			//db_cur->table = NULL; // 첫번째 tb 은 없다.

			break;

		}

		*/



		if (strcmp(cur_tb->tb_name, tbname) == 0) { // 이름이 같냐



			find_flag = 1;



			//처음일 때

			if (cur_tb == db_cur->table) { // 첫번째 tb

				db_cur->table = cur_tb->t_next;

				delete_tb_all(cur_tb);

				free(cur_tb);

			}

			else {//처음 이외 (중간, 마지막 포함)

				pre_cur_tb->t_next = cur_tb->t_next;

				delete_tb_all(cur_tb);

				free(cur_tb);

			}



			break;



		}



		pre_cur_tb = cur_tb;

		cur_tb = cur_tb->t_next;



	}



	if (find_flag == 1) {

		printf("delete tb sucess");

		printf("\n");

		printf("***********************\n");

	}

	else {

		printf("Table[%s] not fount" , tbname);

		printf("\n");

		printf("***********************\n");

	}

}

int create_col(char* new_char) {

	int count = 1;

	//printf("넘겨받은 인자 : %s\n", new_char);

	char* ctr = strtok(new_char, " ");

	COLUMN* col1 = malloc(sizeof(COLUMN));

	col1->col_next = NULL;

	col1->data = NULL;

	char is_num[100];

	int num_flag = 0;

	while (ctr != NULL)

	{

		switch (count)

		{

		case(1):

			strcpy(col1->col_char, ctr);// 삽입

			count++;

			printf("%s  ", col1->col_char);

			break;

		case(2):

			if (strcmp(ctr, "VARCHAR") == 0 || strcmp(ctr, "INT") == 0 || strcmp(ctr, "FLOAT") == 0){

				strcpy(col1->col_type, ctr);// 삽입

				count++;

				//printf("두번째 자른 인수%s\n", col1->col_type);

				break;

			}

			else

			{

				printf("type error\n");

				return 0;

			}

		case(3):

			strcpy(is_num, ctr);

			for (int i = 0; i < strlen(ctr); i++)

			{



				if (isdigit(is_num[i]) == 0) {

					num_flag = 1;

				}

			}

			if (num_flag != 1) {

				strcpy(col1->col_size, ctr);// 삽입

				count = 1;

				break;

				//printf("세번째 자른 인수%s\n", col1->col_size);

			}

			else

			{

				printf("num error\n");

				return 0;

			}

			num_flag = 0;

		}

		ctr = strtok(NULL, " ");

	}

	//free(tb_col);



	if (tb_cur->column == NULL) {

		tb_cur->column = col1;

	}

	else

	{

		COLUMN* cur = tb_cur->column;

		while (cur->col_next != NULL) {

			cur = cur->col_next;

		}

		cur->col_next = col1;

	}



	return 1;



}

void insert_data(char* new_data) {

	char* ptr = strtok(new_data, " ");//테이블 명 strtok

	//printf("ptr :%s", ptr); //테이블 명

	TABLE* cur_tb = db_cur->table;

	while (1)

	{

		if (strcmp(cur_tb->tb_name, ptr) == 0) {//테이블명 비교

			//printf("data 값은%s\n", new_data);

			tb_cur = cur_tb;

			break;

		}

		cur_tb = cur_tb->t_next;

	}



	ptr = strtok(NULL, "(");//테이블 명 strtok



	if (strcmp(ptr, "VALUES") == 0) {

		ptr = strtok(NULL, ",");//테이블 명 strtok

		COLUMN* cur_col = tb_cur->column;



		while (ptr != NULL)

		{

			DATA* data1 = malloc(sizeof(DATA));

			data1->da_next = NULL;

			

			//printf("type:[%s]\n", cur_col->col_type);

			

			if (atoi(cur_col->col_size) >= strlen(ptr)) {

				strcpy(data1->da_value, ptr);

			}

			else

			{

				printf("문자의 길이 error\n");

				return;

			}

			



			if (cur_col->data == NULL) {

				cur_col->data = data1;

			}

			else

			{

				DATA* cur = cur_col->data;

				while (cur->da_next != NULL) {

					cur = cur->da_next;

				}

				cur->da_next = data1;

			}

			cur_col = cur_col->col_next;

			ptr = strtok(NULL, " ");

			ptr = strtok(ptr, ",)");

		}

		

		printf("sucess\n");

		printf("*****************************\n");

	}

}

void show_db() {

	DB* cur_db = head;    // 연결 리스트 순회용 포인터에 첫 번째 노드의 주소 저장

	printf("*************************\n");

	printf("DB list\n");

	while (cur_db != NULL)               // 포인터가 NULL이 아닐 때 계속 반복

	{

		printf("%s\n", cur_db->db_name);    // 현재 노드의 데이터 출력

		cur_db = cur_db->d_next;             // 포인터에 다음 노드의 주소 저장

	}

	printf("*************************\n");

}

void show_tb() {

	struct TABLE* cur_tb = db_cur->table;    // 연결 리스트 순회용 포인터에 첫 번째 노드의 주소 저장

	printf("*************************\n");

	printf("TABLE list\n");

	while (cur_tb != NULL)               // 포인터가 NULL이 아닐 때 계속 반복

	{

		printf("%s\n", cur_tb->tb_name);    // 현재 노드의 데이터 출력

		//printf();

		cur_tb = cur_tb->t_next;             // 포인터에 다음 노드의 주소 저장

	}

	printf("*************************\n");

}

void desc_tb(char* new_tb) {

	tb_cur = db_cur->table;

	while (1)

	{

		if (strcmp(tb_cur->tb_name, new_tb) == 0) {

			break;

		}

		tb_cur = tb_cur->t_next;

	}

	//tb_cur은 현재 선택된 테이블

	COLUMN* cur_col = tb_cur->column;

	while (cur_col != NULL)

	{

		printf("%s ", cur_col->col_char);

		printf("%s ", cur_col->col_size);

		printf("%s\n", cur_col->col_type);

		cur_col = cur_col->col_next;

	}

	printf("*************************\n");



}

void select_data(char* new_tb) {



	char col_where[100] = { '\0', };

	strcpy(col_where, new_tb);

	char col[100] = { '\0', };

	strcpy(col, new_tb);

	//printf("col : %s\n", col);





	char* next = NULL;



	char* ptr = strtok(col, " ");

	//printf("ptr : %s\n", ptr);

	next = strtok(NULL, "");





	char next_data[100] = { '\0', };



	strcpy(next_data, next);

	//printf("next_data : %s\n", next_data);



	next = strtok(next_data, " ");





	next = strtok(NULL, " ");

	//printf("테이블명 : %s\n", next);



	TABLE* cur_tb = db_cur->table;

	while (1)

	{

		if (strcmp(cur_tb->tb_name, next) == 0) {//테이블명 비교

			tb_cur = cur_tb; //tb_cur이 현재 테이블

			break;

		}

		cur_tb = cur_tb->t_next;

	}

	//printf("현재 table name: %s\n", tb_cur->tb_name);



	next = strtok(NULL, " ");//나머지값

	//printf("받은값 : %s\n", next);





	ptr = strtok(ptr, ",");

	//printf("next : %s\n", ptr);



	int dir_index = 0;

	char* col_dir[100] = { '\0', };

	if (strcmp(ptr, "*") == 0)

	{

		COLUMN* cur_data = tb_cur->column;

		while (cur_data != NULL)

		{

			col_dir[dir_index++] = cur_data->col_char;

			cur_data = cur_data->col_next;

		}



	}

	else {

		while (ptr != NULL)//컬럼값 포인터 배열에 넣기

		{



			//printf("col : %s\n", ptr);

			col_dir[dir_index++] = ptr;

			ptr = strtok(NULL, ",");

		}

	}

	if (next == NULL)

	{

		DATA* cur_d = cur_tb->column->data;

		printf("*************************\n");

		printf("data list\n");



		int row1 = 0; //행

		int col1 = 0; //열



		int j = 0;

		while (col_dir[j] != NULL)

		{

			col1++;

			j++;

		}

		while (cur_d != NULL)

		{

			cur_d = cur_d->da_next;//다음 데이터로 옮김

			row1++;

		}

		printf("row : %d\n", row1);

		printf("col : %d\n", col1);



		///*



		COLUMN* cur_data = tb_cur->column; //첫번째 col_char를 가르키게 만듬

		while (1)

		{

			if (strcmp(col_dir[0], cur_data->col_char) == 0) {

				break;

			}

			cur_data = cur_data->col_next;

		}

		COLUMN* cur_da = cur_data; //cur_da는 현재 뽑아야할 컬럼

		cur_d = db_cur->table->column->data;//초기화



		for (int i = 0; i < row1; i++) {



			for (int j = 0; j < col1; j++)

			{

				if (cur_da == NULL) {

					break;

				}

				else {

					cur_d = cur_da->data;



					for (int x = 0; x < i; x++)

					{

						cur_d = cur_d->da_next;

					}

				}

				printf("%s ", cur_d->da_value);

				cur_da = cur_da->col_next;



				while (cur_da != NULL)

				{

					int flag = 0;

					for (int col_dir_index = 0; col_dir_index < dir_index; col_dir_index++) { // 있는지없는지 학인

						if (strcmp(col_dir[col_dir_index], cur_da->col_char) == 0) {

							flag = 1;

						}

					}

					if (flag == 1) {

						//cur_da = cur_da->col_next;//다음 컬럼으로 옮김

						break;

					}

					else {

						cur_da = cur_da->col_next;

					}

				}



			}

			cur_da = cur_data;//초기화

			cur_d = db_cur->table->column->data;//초기화

			printf("\n");

		}

		printf("*************************\n");

	}

	else

	{

		//printf("col_where :%s\n", col_where);



		////tb_cur 현재 테이블 말함

		////tb_cur

		char* next_col_where = NULL;



		next_col_where = strtok(col_where, " ");

		next_col_where = strtok(NULL, " ");

		next_col_where = strtok(NULL, " ");

		next_col_where = strtok(NULL, " ");

		next_col_where = strtok(NULL, " ");



		//printf("컬럼1 :%s\n", next_col_where); //WHERE 이후 대상컬럼 이름



		COLUMN* where_after_col = tb_cur->column;

		while (1)

		{

			if (strcmp(where_after_col->col_char, next_col_where) == 0) {

				break;

			}

			where_after_col = where_after_col->col_next;

		}

		next_col_where = strtok(NULL, " ");

		//printf("컬럼2 :%s\n", next_col_where); //WHERE 이후 대상컬럼 이름





		int data_index[100] = { 0, };

		int data_index_total[100] = { 0, };

		if (strcmp(next_col_where, "=") == 0)

		{

			next_col_where = strtok(NULL, " ");

			//printf("컬럼3 :%s\n", next_col_where); //WHERE 이후 데이터 값





			DATA* where_after_data = where_after_col->data;

			int where_after_data_count = 1;





			int data_index_count = 0;



			while (where_after_data != NULL)

			{

				if (strcmp(where_after_data->da_value, next_col_where) == 0) {

					data_index[data_index_count++] = where_after_data_count;

				}

				where_after_data_count++;

				where_after_data = where_after_data->da_next;

			}





			int good = 0; //데이터 인덱스 확인

			while (data_index[good] != NULL)

			{

				//printf("%d\n", data_index[good]);

				good++;

			}





		}

		else if (strcmp(next_col_where, "!=") == 0) {



			next_col_where = strtok(NULL, " ");

			//printf("컬럼3 :%s\n", next_col_where); //WHERE 이후 데이터 값



			DATA* where_after_data = where_after_col->data;

			int where_after_data_count = 1;





			int data_index_count = 0;



			while (where_after_data != NULL)

			{

				if ((strcmp(where_after_data->da_value, next_col_where) == 1) || (strcmp(where_after_data->da_value, next_col_where) == -1)) {

					data_index[data_index_count++] = where_after_data_count;

				}

				where_after_data_count++;

				where_after_data = where_after_data->da_next;

			}





			int good = 0; //데이터 인덱스 확인

			while (data_index[good] != NULL)

			{

				//printf("%d\n", data_index[good]);

				good++;

			}

		}



		next_col_where = strtok(NULL, " ");

		if (next_col_where == NULL)

		{

			DATA* cur_d = cur_tb->column->data;

			printf("*************************\n");

			printf("data list\n");



			int row1 = 0; //행

			int col1 = 0; //열



			int j = 0;

			while (col_dir[j++] != NULL)

			{

				col1++;

			}

			j = 0;

			while (data_index[j++] != NULL)

			{

				row1++;

			}



			printf("row : %d\n", row1);

			printf("col : %d\n", col1);



			COLUMN* cur_data = tb_cur->column; //첫번째 col_char를 가르키게 만듬

			while (1)

			{

				if (strcmp(col_dir[0], cur_data->col_char) == 0) {

					break;

				}

				cur_data = cur_data->col_next;

			}



			COLUMN* cur_da = cur_data; //cur_da는 현재 뽑아야할 컬럼

			cur_d = db_cur->table->column->data;//초기화



			int good_index = 0;



			for (int i = 0; i < row1; i++) {



				for (int j = 0; j < col1; j++)

				{

					if (cur_da == NULL) {

						break;

					}

					else {



						cur_d = cur_da->data;



						for (int x = 1; x < data_index[good_index]; x++)

						{

							cur_d = cur_d->da_next;

						}

					}

					printf("%s ", cur_d->da_value);

					cur_da = cur_da->col_next;



					while (cur_da != NULL)

					{

						int flag = 0;

						for (int col_dir_index = 0; col_dir_index < dir_index; col_dir_index++) { // 있는지없는지 학인

							if (strcmp(col_dir[col_dir_index], cur_da->col_char) == 0) {

								flag = 1;

							}

						}

						if (flag == 1) {

							//cur_da = cur_da->col_next;//다음 컬럼으로 옮김

							break;

						}

						else {

							cur_da = cur_da->col_next;

						}

					}

				}

				good_index++;

				cur_da = cur_data;//초기화

				cur_d = db_cur->table->column->data;//초기화

				printf("\n");

			}

			printf("*************************\n");

		}

		else if (strcmp(next_col_where, "AND") == 0)

		{

			//printf("AND라면 : % s\n", next_col_where); //WHERE 이후 데이터 값



			next_col_where = strtok(NULL, " ");

			//printf("컬럼1 :%s\n", next_col_where); //WHERE 이후 데이터 값next_col_where = strtok(NULL, " ");

			COLUMN* where_after_second_col = tb_cur->column;

			while (1)

			{

				if (strcmp(where_after_second_col->col_char, next_col_where) == 0) {

					break;

				}

				where_after_second_col = where_after_second_col->col_next;

			}

			//printf("컬럼 네임:%s\n", where_after_second_col->col_char);





			next_col_where = strtok(NULL, " ");

			//printf("컬럼2 :%s\n", next_col_where); //WHERE 이후 데이터 값next_col_where = strtok(NULL, " ");

			if (strcmp(next_col_where, "=") == 0)

			{

				next_col_where = strtok(NULL, " ");

				//printf("컬럼3 :%s\n", next_col_where); //WHERE 이후 데이터 값





				DATA* where_after_data = where_after_second_col->data;

				int where_after_data_count = 1;



				int data_index_count = 0;

				int data_index_second[100] = { 0, };

				while (where_after_data != NULL)

				{

					if (strcmp(where_after_data->da_value, next_col_where) == 0) {

						data_index_second[data_index_count++] = where_after_data_count;

					}

					where_after_data_count++;

					where_after_data = where_after_data->da_next;

				}

				int first_index = 0; //데이터 인덱스 확인

				while (data_index[first_index] != NULL)

				{

					//printf("첫번째%d\n", data_index[first_index]);

					first_index++;

				}



				int second_index = 0; //데이터 인덱스 확인

				while (data_index_second[second_index] != NULL)

				{

					//printf("두번째%d\n", data_index_second[second_index]);

					second_index++;

				}

				int total_num = 0;

				for (int i = 0; i < first_index; i++)

				{

					for (int j = 0; j < second_index; j++) {

						if (data_index[i] == data_index_second[j]) {

							//printf("같은값%d\n", data_index[i]);

							data_index_total[total_num++] = data_index[i];



						}

					}

				}

			}

			else if (strcmp(next_col_where, "!=") == 0)

			{



				next_col_where = strtok(NULL, " ");

				//printf("컬럼3 :%s\n", next_col_where); //WHERE 이후 데이터 값



				DATA* where_after_data = where_after_second_col->data;

				int where_after_data_count = 1;



				int data_index_second[100] = { 0, };

				int data_index_count = 0;



				while (where_after_data != NULL)

				{

					if ((strcmp(where_after_data->da_value, next_col_where) == 1) || (strcmp(where_after_data->da_value, next_col_where) == -1)) {

						data_index_second[data_index_count++] = where_after_data_count;

					}

					where_after_data_count++;

					where_after_data = where_after_data->da_next;

				}



				int first_index = 0; //데이터 인덱스 확인

				while (data_index[first_index] != NULL)

				{

					//printf("첫번째%d\n", data_index[first_index]);

					first_index++;

				}



				int second_index = 0; //데이터 인덱스 확인

				while (data_index_second[second_index] != NULL)

				{

					//printf("두번째%d\n", data_index_second[second_index]);

					second_index++;

				}

				int total_num = 0;

				for (int i = 0; i < first_index; i++)

				{

					for (int j = 0; j < second_index; j++) {

						if (data_index[i] == data_index_second[j]) {

							data_index_total[total_num++] = data_index[i];

							//printf("같은값%d\n", data_index[i]);

						}

					}

				}



			}

			DATA* cur_d = cur_tb->column->data;

			printf("*************************\n");

			printf("data list\n");



			int row1 = 0; //행

			int col1 = 0; //열



			int j = 0;

			while (col_dir[j++] != NULL)

			{

				col1++;

			}

			j = 0;

			while (data_index_total[j++] != NULL)

			{

				row1++;

			}

			printf("row : %d\n", row1);

			printf("col : %d\n", col1);



			COLUMN* cur_data = tb_cur->column; //첫번째 col_char를 가르키게 만듬

			while (1)

			{

				if (strcmp(col_dir[0], cur_data->col_char) == 0) {

					break;

				}

				cur_data = cur_data->col_next;

			}



			COLUMN* cur_da = cur_data; //cur_da는 현재 뽑아야할 컬럼

			cur_d = db_cur->table->column->data;//초기화



			int good_index = 0;



			for (int i = 0; i < row1; i++) {



				for (int j = 0; j < col1; j++)

				{

					if (cur_da == NULL) {

						break;

					}

					else {



						cur_d = cur_da->data;



						for (int x = 1; x < data_index_total[good_index]; x++)

						{

							cur_d = cur_d->da_next;

						}

					}

					printf("%s ", cur_d->da_value);

					cur_da = cur_da->col_next;



					while (cur_da != NULL)

					{

						int flag = 0;

						for (int col_dir_index = 0; col_dir_index < dir_index; col_dir_index++) { // 있는지없는지 학인

							if (strcmp(col_dir[col_dir_index], cur_da->col_char) == 0) {

								flag = 1;

							}

						}

						if (flag == 1) {

							//cur_da = cur_da->col_next;//다음 컬럼으로 옮김

							break;

						}

						else {

							cur_da = cur_da->col_next;

						}

					}

				}

				good_index++;

				cur_da = cur_data;//초기화

				cur_d = db_cur->table->column->data;//초기화

				printf("\n");

			}

			printf("*************************\n");







		}

		else if (strcmp(next_col_where, "OR") == 0)

		{

			printf("OR이라면 : % s\n", next_col_where); //WHERE 이후 데이터 값

			next_col_where = strtok(NULL, " ");

			printf("컬럼1 :%s\n", next_col_where); //WHERE 이후 데이터 값next_col_where = strtok(NULL, " ");

			COLUMN* where_after_second_col = tb_cur->column;

			while (1)

			{

				if (strcmp(where_after_second_col->col_char, next_col_where) == 0) {

					break;

				}

				where_after_second_col = where_after_second_col->col_next;

			}

			printf("컬럼 네임:%s\n", where_after_second_col->col_char);





			next_col_where = strtok(NULL, " ");

			printf("컬럼2 :%s\n", next_col_where); //WHERE 이후 데이터 값next_col_where = strtok(NULL, " ");

			if (strcmp(next_col_where, "=") == 0)

			{

				next_col_where = strtok(NULL, " ");

				printf("컬럼3 :%s\n", next_col_where); //WHERE 이후 데이터 값





				DATA* where_after_data = where_after_second_col->data;

				int where_after_data_count = 1;





				int data_index_count = 0;

				int data_index_second[100] = { 0, };

				while (where_after_data != NULL)

				{

					if (strcmp(where_after_data->da_value, next_col_where) == 0) {

						data_index_second[data_index_count++] = where_after_data_count;

					}

					where_after_data_count++;

					where_after_data = where_after_data->da_next;

				}

				int first_index = 0; //데이터 인덱스 확인

				while (data_index[first_index] != NULL)

				{

					printf("첫번째%d\n", data_index[first_index]);

					first_index++;

				}



				int second_index = 0; //데이터 인덱스 확인

				while (data_index_second[second_index] != NULL)

				{

					printf("두번째%d\n", data_index_second[second_index]);

					second_index++;

				}

				int total_num = 0;

				for (int i = 0; i < first_index; i++)

				{

					for (int j = 0; j < second_index; j++) {



						if (data_index[i] == data_index_second[j]) {

							printf("1같은값%d\n", data_index[i]);

							data_index_total[total_num++] = data_index[i];

							//total_count++;

							break;

						}

						else if (data_index[i] != data_index_second[j]) {

							printf("2같은값%d\n", data_index[i]);

							data_index_total[total_num++] = data_index[i];

							//total_count++;

							break;

						}

					}

				}

				int double_num = 0;

				for (int i = 0; i < second_index; i++)

				{

					for (int j = 0; j < first_index; j++) {



						if (data_index_second[i] == data_index[j]) {

							double_num = 0;

							break;

						}

						else if (data_index_second[i] != data_index[j]) {

							double_num = 1;



						}

					}

					if (double_num == 1) {

						data_index_total[total_num++] = data_index_second[i];

						printf("3같은값%d\n", data_index_second[i]);



						double_num = 0;

					}



				}

				int temp = 0;

				for (int i = 0; i < total_num; i++) {

					for (int j = 0; j < (total_num - 1) - i; j++) {

						if (data_index_total[j] > data_index_total[j + 1]) {	// 버블 정렬 사용

							temp = data_index_total[j];

							data_index_total[j] = data_index_total[j + 1];

							data_index_total[j + 1] = temp;

						}

					}

				}



			}

			else if (strcmp(next_col_where, "!=") == 0)

			{



				next_col_where = strtok(NULL, " ");

				printf("컬럼3 :%s\n", next_col_where); //WHERE 이후 데이터 값



				DATA* where_after_data = where_after_second_col->data;

				int where_after_data_count = 1;



				int data_index_second[100] = { 0, };

				int data_index_count = 0;



				while (where_after_data != NULL)

				{

					if ((strcmp(where_after_data->da_value, next_col_where) == 1) || (strcmp(where_after_data->da_value, next_col_where) == -1)) {

						data_index_second[data_index_count++] = where_after_data_count;

					}

					where_after_data_count++;

					where_after_data = where_after_data->da_next;

				}



				int first_index = 0; //데이터 인덱스 확인

				while (data_index[first_index] != NULL)

				{

					printf("첫번째%d\n", data_index[first_index]);

					first_index++;

				}



				int second_index = 0; //데이터 인덱스 확인

				while (data_index_second[second_index] != NULL)

				{

					printf("두번째%d\n", data_index_second[second_index]);

					second_index++;

				}

				//int total_count = 0;

				int total_num = 0;

				for (int i = 0; i < first_index; i++)

				{

					for (int j = 0; j < second_index; j++) {



						if (data_index[i] == data_index_second[j]) {

							printf("1같은값%d\n", data_index[i]);

							data_index_total[total_num++] = data_index[i];

							//total_count++;

							break;

						}

						else if (data_index[i] != data_index_second[j]) {

							printf("2같은값%d\n", data_index[i]);

							data_index_total[total_num++] = data_index[i];

							//total_count++;

							break;

						}

					}

				}

				int double_num = 0;

				for (int i = 0; i < second_index; i++)

				{

					for (int j = 0; j < first_index; j++) {



						if (data_index_second[i] == data_index[j]) {

							double_num = 0;

							break;

						}

						else if (data_index_second[i] != data_index[j]) {

							double_num = 1;



						}

					}

					if (double_num == 1) {

						data_index_total[total_num++] = data_index_second[i];

						printf("3같은값%d\n", data_index_second[i]);



						double_num = 0;

					}



				}

				int temp = 0;

				for (int i = 0; i < total_num; i++) {

					for (int j = 0; j < (total_num - 1) - i; j++) {

						if (data_index_total[j] > data_index_total[j + 1]) {	// 버블 정렬 사용

							temp = data_index_total[j];

							data_index_total[j] = data_index_total[j + 1];

							data_index_total[j + 1] = temp;

						}

					}

				}



			}

			DATA* cur_d = cur_tb->column->data;

			printf("*************************\n");

			printf("data list\n");



			int row1 = 0; //행

			int col1 = 0; //열



			int j = 0;

			while (col_dir[j++] != NULL)

			{

				col1++;

			}

			j = 0;

			while (data_index_total[j++] != NULL)

			{

				row1++;

			}

			printf("row : %d\n", row1);

			printf("col : %d\n", col1);



			COLUMN* cur_data = tb_cur->column; //첫번째 col_char를 가르키게 만듬

			while (1)

			{

				if (strcmp(col_dir[0], cur_data->col_char) == 0) {

					break;

				}

				cur_data = cur_data->col_next;

			}



			COLUMN* cur_da = cur_data; //cur_da는 현재 뽑아야할 컬럼

			cur_d = db_cur->table->column->data;//초기화



			int good_index = 0;



			for (int i = 0; i < row1; i++) {



				for (int j = 0; j < col1; j++)

				{

					if (cur_da == NULL) {

						break;

					}

					else {



						cur_d = cur_da->data;



						for (int x = 1; x < data_index_total[good_index]; x++)

						{

							cur_d = cur_d->da_next;

						}

					}

					printf("%s ", cur_d->da_value);

					cur_da = cur_da->col_next;



					while (cur_da != NULL)

					{

						int flag = 0;

						for (int col_dir_index = 0; col_dir_index < dir_index; col_dir_index++) { // 있는지없는지 학인

							if (strcmp(col_dir[col_dir_index], cur_da->col_char) == 0) {

								flag = 1;

							}

						}

						if (flag == 1) {

							//cur_da = cur_da->col_next;//다음 컬럼으로 옮김

							break;

						}

						else {

							cur_da = cur_da->col_next;

						}

					}

				}

				good_index++;

				cur_da = cur_data;//초기화

				cur_d = db_cur->table->column->data;//초기화

				printf("\n");

			}

			printf("*************************\n");

		}

	}

}

void delete_table(char* new_tb) {



	char delete_where[100] = { '\0', };//나머지값 전체 복사

	strcpy(delete_where, new_tb);

	



	char *next = strtok(new_tb, " ");

	//printf("테이블 명%s\n", next);



	char* next_col_where = strtok(delete_where, " ");

	next_col_where = strtok(NULL, " "); //ptr=where

	



	TABLE* cur_tb = db_cur->table;

	while (1)

	{

		if (strcmp(cur_tb->tb_name, next) == 0) {

			tb_cur = cur_tb;

			break;

		}

		cur_tb = cur_tb->t_next;

	}

	//printf("%s\n", cur_tb->tb_name);



	if(next_col_where == NULL) //where이 없을때

	{

		delete_tb_all(cur_tb);



		printf("delete sucess\n");

		printf("*************************\n");

	}

	else

	{



		int data_index[100] = { 0, };

		int data_index_total[100] = { 0, };

		//printf("col_where :%s\n", col_where);



		////tb_cur 현재 테이블 말함

		////tb_cur



		next_col_where = strtok(NULL, " ");

		COLUMN* cur_col = cur_tb->column;

		//printf("컬럼1 :%s\n", next_col_where); //WHERE 이후 대상컬럼 이름

		while (1)

		{

			if (strcmp(cur_col->col_char, next_col_where) == 0) {

				col_cur= cur_col;

				break;

			}

			cur_col = cur_col->col_next;

		}

		

		next_col_where = strtok(NULL, " ");

		//printf("컬럼2 :%s\n", next_col_where); //WHERE 이후 대상컬럼 이름



		if (strcmp(next_col_where, "=") == 0)

		{

			next_col_where = strtok(NULL, " ");

			//printf("컬럼3 :%s\n", next_col_where); //WHERE 이후 데이터 값





			DATA* where_after_data = col_cur->data;

			int where_after_data_count = 1;





			int data_index_count = 0;



			while (where_after_data != NULL)

			{

				if (strcmp(where_after_data->da_value, next_col_where) == 0) {

					data_index[data_index_count++] = where_after_data_count;

				}

				where_after_data_count++;

				where_after_data = where_after_data->da_next;

			}





			int good = 0; //데이터 인덱스 확인

			while (data_index[good] != NULL)

			{

				//printf("%d\n", data_index[good]);

				good++;

			}





		}

		else if (strcmp(next_col_where, "!=") == 0) {



			next_col_where = strtok(NULL, " ");

			//printf("컬럼3 :%s\n", next_col_where); //WHERE 이후 데이터 값



			DATA* where_after_data = col_cur->data;

			int where_after_data_count = 1;





			int data_index_count = 0;



			while (where_after_data != NULL)

			{

				if ((strcmp(where_after_data->da_value, next_col_where) == 1) || (strcmp(where_after_data->da_value, next_col_where) == -1)) {

					data_index[data_index_count++] = where_after_data_count;

				}

				where_after_data_count++;

				where_after_data = where_after_data->da_next;

			}





			int good = 0; //데이터 인덱스 확인

			while (data_index[good] != NULL)

			{

				//printf("%d\n", data_index[good]);

				good++;

			}

		}



		next_col_where = strtok(NULL, " ");

		if (next_col_where == NULL)

		{

			//처음 조건 받을때

			////

			//COLUMN* cur_col = tb_cur->column;



			

			////

			printf("*************************\n");

			printf("delete data list\n");



			int row = 0; //행

			int col = 0; //열



			int j = 0;

			col_cur = tb_cur->column;

			while (col_cur != NULL)

			{

				col_cur = col_cur->col_next;

				col++;

			}

			j = 0;

			while (data_index[j++] != NULL)

			{

				row++;

			}



			printf("row : %d\n", row);

			printf("col : %d\n", col);



			COLUMN* cur_da = tb_cur->column; //cur_da는 현재 뽑아야할 컬럼

			DATA* cur_d = cur_da->data;

			DATA* cur_data_pre = cur_da->data;

			int good_index = 0;

			DATA* cur_pre = cur_d;

			int indexcount = 0;

			for (int i = 0; i < row; i++) {



				for (int j = 0; j < col; j++)

				{

					if (cur_da == NULL) {

						break;

					}

					else {



						cur_d = cur_da->data;

						cur_pre = cur_d;



						//printf("index:%d\n", data_index[good_index]);

						for (int x = 1; x < data_index[good_index]; x++)

						{

							cur_d = cur_d->da_next;

						}

						for (int x = 2; x < data_index[good_index]; x++)

						{

							cur_pre = cur_pre->da_next;

						}

						//printf("행 :%d\n", data_index[good_index]);

					}

					if (cur_da->data==cur_d) {//처음 인덱스 제거

						cur_da->data = cur_d->da_next;

						printf("%s", cur_d->da_value);

						free(cur_d);

					}

					else if (cur_d->da_next ==NULL) {//마지막 인덱스 제거

						cur_pre->da_next = NULL;

						//printf("*%s", cur_pre->da_value);

						printf("%s", cur_d->da_value);

						free(cur_d);

					}

					else//중간값 제거

					{

						cur_pre->da_next = cur_d->da_next;

						//printf("!%s", cur_pre->da_value);

						printf("%s", cur_d->da_value);

						free(cur_d);

					}



					cur_da = cur_da->col_next;



				}

				indexcount++;

				good_index++;

				data_index[good_index]=data_index[good_index] - indexcount;

				cur_da = tb_cur->column;//초기화

				printf("\n");

			}

			printf("*************************\n");





		}

		else if (strcmp(next_col_where, "AND") == 0)

		{

			//printf("AND라면 : % s\n", next_col_where); //WHERE 이후 데이터 값



			next_col_where = strtok(NULL, " ");

			//printf("컬럼1 :%s\n", next_col_where); //WHERE 이후 데이터 값next_col_where = strtok(NULL, " ");

			COLUMN* where_after_second_col = tb_cur->column;

			while (1)

			{

				if (strcmp(where_after_second_col->col_char, next_col_where) == 0) {

					break;

				}

				where_after_second_col = where_after_second_col->col_next;

			}

			//printf("컬럼 네임:%s\n", where_after_second_col->col_char);





			next_col_where = strtok(NULL, " ");

			//printf("컬럼2 :%s\n", next_col_where); //WHERE 이후 데이터 값next_col_where = strtok(NULL, " ");

			if (strcmp(next_col_where, "=") == 0)

			{

				next_col_where = strtok(NULL, " ");

				//printf("컬럼3 :%s\n", next_col_where); //WHERE 이후 데이터 값





				DATA* where_after_data = where_after_second_col->data;

				int where_after_data_count = 1;



				int data_index_count = 0;

				int data_index_second[100] = { 0, };

				while (where_after_data != NULL)

				{

					if (strcmp(where_after_data->da_value, next_col_where) == 0) {

						data_index_second[data_index_count++] = where_after_data_count;

					}

					where_after_data_count++;

					where_after_data = where_after_data->da_next;

				}

				int first_index = 0; //데이터 인덱스 확인

				while (data_index[first_index] != NULL)

				{

					//printf("첫번째%d\n", data_index[first_index]);

					first_index++;

				}



				int second_index = 0; //데이터 인덱스 확인

				while (data_index_second[second_index] != NULL)

				{

					//printf("두번째%d\n", data_index_second[second_index]);

					second_index++;

				}

				int total_num = 0;

				for (int i = 0; i < first_index; i++)

				{

					for (int j = 0; j < second_index; j++) {

						if (data_index[i] == data_index_second[j]) {

							//printf("같은값%d\n", data_index[i]);

							data_index_total[total_num++] = data_index[i];



						}

					}

				}

			}

			else if (strcmp(next_col_where, "!=") == 0)

			{



				next_col_where = strtok(NULL, " ");

				//printf("컬럼3 :%s\n", next_col_where); //WHERE 이후 데이터 값



				DATA* where_after_data = where_after_second_col->data;

				int where_after_data_count = 1;



				int data_index_second[100] = { 0, };

				int data_index_count = 0;



				while (where_after_data != NULL)

				{

					if ((strcmp(where_after_data->da_value, next_col_where) == 1) || (strcmp(where_after_data->da_value, next_col_where) == -1)) {

						data_index_second[data_index_count++] = where_after_data_count;

					}

					where_after_data_count++;

					where_after_data = where_after_data->da_next;

				}



				int first_index = 0; //데이터 인덱스 확인

				while (data_index[first_index] != NULL)

				{

					//printf("첫번째%d\n", data_index[first_index]);

					first_index++;

				}



				int second_index = 0; //데이터 인덱스 확인

				while (data_index_second[second_index] != NULL)

				{

					//printf("두번째%d\n", data_index_second[second_index]);

					second_index++;

				}

				int total_num = 0;

				for (int i = 0; i < first_index; i++)

				{

					for (int j = 0; j < second_index; j++) {

						if (data_index[i] == data_index_second[j]) {

							data_index_total[total_num++] = data_index[i];

							//printf("같은값%d\n", data_index[i]);

						}

					}

				}



			}



			//and일때 



			printf("*************************\n");

			printf("data list\n");



			int row = 0; //행

			int col = 0; //열



			int j = 0;

			col_cur = tb_cur->column;

			while (col_cur != NULL)

			{

				col_cur = col_cur->col_next;

				col++;

			}

			j = 0;

			while (data_index_total[j++] != NULL)

			{

				row++;

			}



			printf("row : %d\n", row);

			printf("col : %d\n", col);



			COLUMN* cur_da = tb_cur->column; //cur_da는 현재 뽑아야할 컬럼

			DATA* cur_d = cur_da->data;

			DATA* cur_data_pre = cur_da->data;

			int good_index = 0;

			DATA* cur_pre = cur_d;

			int indexcount = 0;

			for (int i = 0; i < row; i++) {



				for (int j = 0; j < col; j++)

				{

					if (cur_da == NULL) {

						break;

					}

					else {



						cur_d = cur_da->data;

						cur_pre = cur_d;



						//printf("index:%d\n", data_index[good_index]);

						for (int x = 1; x < data_index_total[good_index]; x++)

						{

							cur_d = cur_d->da_next;

						}

						for (int x = 2; x < data_index_total[good_index]; x++)

						{

							cur_pre = cur_pre->da_next;

						}

						//printf("행 :%d\n", data_index[good_index]);

					}

					if (cur_da->data == cur_d) {//처음 인덱스 제거

						cur_da->data = cur_d->da_next;

						printf("%s", cur_d->da_value);





						free(cur_d);

					}

					else if (cur_d->da_next == NULL) {//마지막 인덱스 제거

						cur_pre->da_next = NULL;

						//printf("*%s", cur_pre->da_value);

						printf("%s", cur_d->da_value);

						free(cur_d);

					}

					else//중간값 제거

					{

						cur_pre->da_next = cur_d->da_next;

						//printf("!%s", cur_pre->da_value);

						printf("%s", cur_d->da_value);



						free(cur_d);

					}



					cur_da = cur_da->col_next;



				}

				indexcount++;

				good_index++;

				data_index_total[good_index] = data_index_total[good_index] - indexcount;

				cur_da = tb_cur->column;//초기화

				printf("\n");

			}

			printf("*************************\n");





		}

		else if (strcmp(next_col_where, "OR") == 0)

		{

			printf("OR이라면 : % s\n", next_col_where); //WHERE 이후 데이터 값

			next_col_where = strtok(NULL, " ");

			//printf("컬럼1 :%s\n", next_col_where); //WHERE 이후 데이터 값next_col_where = strtok(NULL, " ");

			COLUMN* where_after_second_col = tb_cur->column;

			while (1)

			{

				if (strcmp(where_after_second_col->col_char, next_col_where) == 0) {

					break;

				}

				where_after_second_col = where_after_second_col->col_next;

			}

			//printf("컬럼 네임:%s\n", where_after_second_col->col_char);





			next_col_where = strtok(NULL, " ");

			//printf("컬럼2 :%s\n", next_col_where); //WHERE 이후 데이터 값next_col_where = strtok(NULL, " ");

			if (strcmp(next_col_where, "=") == 0)

			{

				next_col_where = strtok(NULL, " ");

				//printf("컬럼3 :%s\n", next_col_where); //WHERE 이후 데이터 값





				DATA* where_after_data = where_after_second_col->data;

				int where_after_data_count = 1;





				int data_index_count = 0;

				int data_index_second[100] = { 0, };

				while (where_after_data != NULL)

				{

					if (strcmp(where_after_data->da_value, next_col_where) == 0)

					{

						data_index_second[data_index_count++] = where_after_data_count;

					}

					where_after_data_count++;

					where_after_data = where_after_data->da_next;

				}

				int first_index = 0; //데이터 인덱스 확인

				while (data_index[first_index] != NULL)

				{

					//printf("첫번째%d\n", data_index[first_index]);

					first_index++;

				}



				int second_index = 0; //데이터 인덱스 확인

				while (data_index_second[second_index] != NULL)

				{

					//printf("두번째%d\n", data_index_second[second_index]);

					second_index++;

				}

				int total_num = 0;

				for (int i = 0; i < first_index; i++)

				{

					for (int j = 0; j < second_index; j++)

					{

						if (data_index[i] == data_index_second[j])

						{

							//printf("1같은값%d\n", data_index[i]);

							data_index_total[total_num++] = data_index[i];

							//total_count++;

							break;

						}

						else if (data_index[i] != data_index_second[j])

						{

							//printf("2같은값%d\n", data_index[i]);

							data_index_total[total_num++] = data_index[i];

							//total_count++;

							break;

						}

					}

				}

				int double_num = 0;

				for (int i = 0; i < second_index; i++)

				{

					for (int j = 0; j < first_index; j++) {



						if (data_index_second[i] == data_index[j]) {

							double_num = 0;

							break;

						}

						else if (data_index_second[i] != data_index[j]) {

							double_num = 1;



						}

					}

					if (double_num == 1) {

						data_index_total[total_num++] = data_index_second[i];

						//printf("3같은값%d\n", data_index_second[i]);



						double_num = 0;

					}



				}

				int temp = 0;

				for (int i = 0; i < total_num; i++)

				{

					for (int j = 0; j < (total_num - 1) - i; j++)

					{

						if (data_index_total[j] > data_index_total[j + 1])

						{	// 버블 정렬 사용

							temp = data_index_total[j];

							data_index_total[j] = data_index_total[j + 1];

							data_index_total[j + 1] = temp;

						}

					}

				}



			}

			else if (strcmp(next_col_where, "!=") == 0)

			{



				next_col_where = strtok(NULL, " ");

				//printf("컬럼3 :%s\n", next_col_where); //WHERE 이후 데이터 값



				DATA* where_after_data = where_after_second_col->data;

				int where_after_data_count = 1;



				int data_index_second[100] = { 0, };

				int data_index_count = 0;



				while (where_after_data != NULL)

				{

					if ((strcmp(where_after_data->da_value, next_col_where) == 1) || (strcmp(where_after_data->da_value, next_col_where) == -1))

					{

						data_index_second[data_index_count++] = where_after_data_count;

					}

					where_after_data_count++;

					where_after_data = where_after_data->da_next;

				}



				int first_index = 0; //데이터 인덱스 확인

				while (data_index[first_index] != NULL)

				{

					//printf("첫번째%d\n", data_index[first_index]);

					first_index++;

				}



				int second_index = 0; //데이터 인덱스 확인

				while (data_index_second[second_index] != NULL)

				{

					//printf("두번째%d\n", data_index_second[second_index]);

					second_index++;

				}

				//int total_count = 0;

				int total_num = 0;

				for (int i = 0; i < first_index; i++)

				{

					for (int j = 0; j < second_index; j++) {



						if (data_index[i] == data_index_second[j]) {

							//printf("1같은값%d\n", data_index[i]);

							data_index_total[total_num++] = data_index[i];

							//total_count++;

							break;

						}

						else if (data_index[i] != data_index_second[j]) {

							//printf("2같은값%d\n", data_index[i]);

							data_index_total[total_num++] = data_index[i];

							//total_count++;

							break;

						}

					}

				}

				int double_num = 0;

				for (int i = 0; i < second_index; i++)

				{

					for (int j = 0; j < first_index; j++) {



						if (data_index_second[i] == data_index[j]) {

							double_num = 0;

							break;

						}

						else if (data_index_second[i] != data_index[j]) {

							double_num = 1;



						}

					}

					if (double_num == 1)

					{

						data_index_total[total_num++] = data_index_second[i];

						printf("3같은값%d\n", data_index_second[i]);



						double_num = 0;

					}



				}

				int temp = 0;

				for (int i = 0; i < total_num; i++)

				{

					for (int j = 0; j < (total_num - 1) - i; j++)

					{

						if (data_index_total[j] > data_index_total[j + 1])

						{	// 버블 정렬 사용

							temp = data_index_total[j];

							data_index_total[j] = data_index_total[j + 1];

							data_index_total[j + 1] = temp;

						}

					}

				}

			}

			printf("*************************\n");

			printf("data list\n");



			int row = 0; //행

			int col = 0; //열



			int j = 0;

			col_cur = tb_cur->column;

			while (col_cur != NULL)

			{

				col_cur = col_cur->col_next;

				col++;

			}

			j = 0;

			while (data_index_total[j++] != NULL)

			{

				row++;

			}



			printf("row : %d\n", row);

			printf("col : %d\n", col);



			COLUMN* cur_da = tb_cur->column; //cur_da는 현재 뽑아야할 컬럼

			DATA* cur_d = cur_da->data;

			DATA* cur_data_pre = cur_da->data;

			int good_index = 0;

			DATA* cur_pre = cur_d;

			int indexcount = 0;

			for (int i = 0; i < row; i++) {



				for (int j = 0; j < col; j++)

				{

					if (cur_da == NULL) {

						break;

					}

					else {



						cur_d = cur_da->data;

						cur_pre = cur_d;



						//printf("index:%d\n", data_index[good_index]);

						for (int x = 1; x < data_index_total[good_index]; x++)

						{

							cur_d = cur_d->da_next;

						}

						for (int x = 2; x < data_index_total[good_index]; x++)

						{

							cur_pre = cur_pre->da_next;

						}

						//printf("행 :%d\n", data_index[good_index]);

					}

					if (cur_da->data == cur_d) {//처음 인덱스 제거

						cur_da->data = cur_d->da_next;

						printf("%s", cur_d->da_value);





						free(cur_d);

					}

					else if (cur_d->da_next == NULL) {//마지막 인덱스 제거

						cur_pre->da_next = NULL;

						//printf("*%s", cur_pre->da_value);

						printf("%s", cur_d->da_value);

						free(cur_d);

					}

					else//중간값 제거

					{

						cur_pre->da_next = cur_d->da_next;

						//printf("!%s", cur_pre->da_value);

						printf("%s", cur_d->da_value);



						free(cur_d);

					}



					cur_da = cur_da->col_next;



				}

				indexcount++;

				good_index++;

				data_index_total[good_index] = data_index_total[good_index] - indexcount;

				cur_da = tb_cur->column;//초기화

				printf("\n");

			}

			printf("*************************\n");

		}

	}

}

void update_data(char* newdata) {

	char update_where[100] = { '\0', };//*이외의 것일때

	strcpy(update_where, newdata);



	//printf("%s\n", newdata);

	char* next = NULL;

	next = strtok(newdata, " ");

	//printf("%s\n", next);



	TABLE* cur_tb = db_cur->table;

	while (1)

	{

		if (strcmp(cur_tb->tb_name, next) == 0) {

			tb_cur = cur_tb;

			break;

		}

		cur_tb = cur_tb->t_next;

	}

	next = strtok(NULL, " ");//set



	next = strtok(NULL, " ");



	COLUMN* cur_col = tb_cur->column;

	while (1)

	{

		if (strcmp(cur_col->col_char, next) == 0) {

			col_cur = cur_col;

			break;

		}

		cur_col = cur_col->col_next;

	}

	next = strtok(NULL, " ");//=

	//if (strcmp(next,"=") == 0)



	next = strtok(NULL, " ");



	//where이후 파싱

	//printf("up%s\n", update_where);

	char* next_col_where = NULL;



	next_col_where = strtok(update_where, " ");//TBNAME

	//printf("where1%s\n", next_col_where);



	next_col_where = strtok(NULL, " ");//SET





	next_col_where = strtok(NULL, " ");//COL NAME

	//printf("where3%s\n", next_col_where);



	next_col_where = strtok(NULL, " ");//=

	next_col_where = strtok(NULL, " ");//11



	/////////////////////

	next_col_where = strtok(NULL, " ");



	if (next_col_where == NULL)

	{

		DATA* cur_data = col_cur->data;



		while (cur_data != NULL)

		{

			printf("%s-> ", cur_data->da_value);

			memset(cur_data->da_value, 0, sizeof(char) * 20);

			strcpy(cur_data->da_value, next);

			printf("%s\n", cur_data->da_value);

			cur_data = cur_data->da_next;

		}

	}

	else

	{

		int data_index[100] = { 0, };

		int data_index_total[100] = { 0, };

		//printf("col_where :%s\n", col_where);



		////tb_cur 현재 테이블 말함

		////tb_cur



		next_col_where = strtok(NULL, " ");



		//printf("컬럼1 :%s\n", next_col_where); //WHERE 이후 대상컬럼 이름



		COLUMN* where_after_col = tb_cur->column;

		while (1)

		{

			if (strcmp(where_after_col->col_char, next_col_where) == 0)

			{

				break;

			}

			where_after_col = where_after_col->col_next;

		}

		next_col_where = strtok(NULL, " ");

		//printf("컬럼2 :%s\n", next_col_where); //WHERE 이후 대상컬럼 이름







		if (strcmp(next_col_where, "=") == 0)

		{

			next_col_where = strtok(NULL, " ");

			//printf("컬럼3 :%s\n", next_col_where); //WHERE 이후 데이터 값





			DATA* where_after_data = where_after_col->data;

			int where_after_data_count = 1;





			int data_index_count = 0;



			while (where_after_data != NULL)

			{

				if (strcmp(where_after_data->da_value, next_col_where) == 0) {

					data_index[data_index_count++] = where_after_data_count;

				}

				where_after_data_count++;

				where_after_data = where_after_data->da_next;

			}





			int good = 0; //데이터 인덱스 확인

			while (data_index[good] != NULL)

			{

				//printf("%d\n", data_index[good]);

				good++;

			}





		}

		else if (strcmp(next_col_where, "!=") == 0) {



			next_col_where = strtok(NULL, " ");

			//printf("컬럼3 :%s\n", next_col_where); //WHERE 이후 데이터 값



			DATA* where_after_data = where_after_col->data;

			int where_after_data_count = 1;





			int data_index_count = 0;



			while (where_after_data != NULL)

			{

				if ((strcmp(where_after_data->da_value, next_col_where) == 1) || (strcmp(where_after_data->da_value, next_col_where) == -1)) {

					data_index[data_index_count++] = where_after_data_count;

				}

				where_after_data_count++;

				where_after_data = where_after_data->da_next;

			}





			int good = 0; //데이터 인덱스 확인

			while (data_index[good] != NULL)

			{

				//printf("%d\n", data_index[good]);

				good++;

			}

		}



		next_col_where = strtok(NULL, " ");

		if (next_col_where == NULL)

		{

			//처음 조건 받을때



			COLUMN* cur_col_first = col_cur; //첫번째 col_char를 가르키게 만듬

			int good_index = 0;



			while (data_index[good_index] !=NULL)

			{

				DATA* cur_data = cur_col_first->data;



				for (int x = 1; x < data_index[good_index]; x++)

				{

					cur_data = cur_data->da_next;

				}

				good_index++;



				printf("%s-> ", cur_data->da_value);

				memset(cur_data->da_value, 0, sizeof(char) * 20);

				strcpy(cur_data->da_value, next);

				printf("%s\n", cur_data->da_value);

			}

		}

		else if (strcmp(next_col_where, "AND") == 0)

		{

			//printf("AND라면 : % s\n", next_col_where); //WHERE 이후 데이터 값



			next_col_where = strtok(NULL, " ");

			//printf("컬럼1 :%s\n", next_col_where); //WHERE 이후 데이터 값next_col_where = strtok(NULL, " ");

			COLUMN* where_after_second_col = tb_cur->column;

			while (1)

			{

				if (strcmp(where_after_second_col->col_char, next_col_where) == 0) {

					break;

				}

				where_after_second_col = where_after_second_col->col_next;

			}

			//printf("컬럼 네임:%s\n", where_after_second_col->col_char);





			next_col_where = strtok(NULL, " ");

			//printf("컬럼2 :%s\n", next_col_where); //WHERE 이후 데이터 값next_col_where = strtok(NULL, " ");

			if (strcmp(next_col_where, "=") == 0)

			{

				next_col_where = strtok(NULL, " ");

				//printf("컬럼3 :%s\n", next_col_where); //WHERE 이후 데이터 값





				DATA* where_after_data = where_after_second_col->data;

				int where_after_data_count = 1;



				int data_index_count = 0;

				int data_index_second[100] = { 0, };

				while (where_after_data != NULL)

				{

					if (strcmp(where_after_data->da_value, next_col_where) == 0) {

						data_index_second[data_index_count++] = where_after_data_count;

					}

					where_after_data_count++;

					where_after_data = where_after_data->da_next;

				}

				int first_index = 0; //데이터 인덱스 확인

				while (data_index[first_index] != NULL)

				{

					//printf("첫번째%d\n", data_index[first_index]);

					first_index++;

				}



				int second_index = 0; //데이터 인덱스 확인

				while (data_index_second[second_index] != NULL)

				{

					//printf("두번째%d\n", data_index_second[second_index]);

					second_index++;

				}

				int total_num = 0;

				for (int i = 0; i < first_index; i++)

				{

					for (int j = 0; j < second_index; j++) {

						if (data_index[i] == data_index_second[j]) {

							//printf("같은값%d\n", data_index[i]);

							data_index_total[total_num++] = data_index[i];



						}

					}

				}

			}

			else if (strcmp(next_col_where, "!=") == 0)

			{



				next_col_where = strtok(NULL, " ");

				//printf("컬럼3 :%s\n", next_col_where); //WHERE 이후 데이터 값



				DATA* where_after_data = where_after_second_col->data;

				int where_after_data_count = 1;



				int data_index_second[100] = { 0, };

				int data_index_count = 0;



				while (where_after_data != NULL)

				{

					if ((strcmp(where_after_data->da_value, next_col_where) == 1) || (strcmp(where_after_data->da_value, next_col_where) == -1)) {

						data_index_second[data_index_count++] = where_after_data_count;

					}

					where_after_data_count++;

					where_after_data = where_after_data->da_next;

				}



				int first_index = 0; //데이터 인덱스 확인

				while (data_index[first_index] != NULL)

				{

					//printf("첫번째%d\n", data_index[first_index]);

					first_index++;

				}



				int second_index = 0; //데이터 인덱스 확인

				while (data_index_second[second_index] != NULL)

				{

					//printf("두번째%d\n", data_index_second[second_index]);

					second_index++;

				}

				int total_num = 0;

				for (int i = 0; i < first_index; i++)

				{

					for (int j = 0; j < second_index; j++) {

						if (data_index[i] == data_index_second[j]) {

							data_index_total[total_num++] = data_index[i];

							//printf("같은값%d\n", data_index[i]);

						}

					}

				}



			}



			//and일때 



			COLUMN* cur_col_first = col_cur; //첫번째 col_char를 가르키게 만듬

			int good_index = 0;



			while (data_index_total[good_index] != NULL)

			{

				DATA* cur_data = cur_col_first->data;



				for (int x = 1; x < data_index_total[good_index]; x++)

				{

					cur_data = cur_data->da_next;

				}

				good_index++;



				printf("%s-> ", cur_data->da_value);

				memset(cur_data->da_value, 0, sizeof(char) * 20);

				strcpy(cur_data->da_value, next);

				printf("%s\n", cur_data->da_value);

			}

		}

		else if (strcmp(next_col_where, "OR") == 0)

		{

			//printf("OR이라면 : % s\n", next_col_where); //WHERE 이후 데이터 값

			next_col_where = strtok(NULL, " ");

			//printf("컬럼1 :%s\n", next_col_where); //WHERE 이후 데이터 값next_col_where = strtok(NULL, " ");

			COLUMN* where_after_second_col = tb_cur->column;

			while (1)

			{

				if (strcmp(where_after_second_col->col_char, next_col_where) == 0) {

					break;

				}

				where_after_second_col = where_after_second_col->col_next;

			}

			//printf("컬럼 네임:%s\n", where_after_second_col->col_char);





			next_col_where = strtok(NULL, " ");

			//printf("컬럼2 :%s\n", next_col_where); //WHERE 이후 데이터 값next_col_where = strtok(NULL, " ");

			if (strcmp(next_col_where, "=") == 0)

			{

				next_col_where = strtok(NULL, " ");

				//printf("컬럼3 :%s\n", next_col_where); //WHERE 이후 데이터 값





				DATA* where_after_data = where_after_second_col->data;

				int where_after_data_count = 1;





				int data_index_count = 0;

				int data_index_second[100] = { 0, };

				while (where_after_data != NULL)

				{

					if (strcmp(where_after_data->da_value, next_col_where) == 0)

					{

						data_index_second[data_index_count++] = where_after_data_count;

					}

					where_after_data_count++;

					where_after_data = where_after_data->da_next;

				}

				int first_index = 0; //데이터 인덱스 확인

				while (data_index[first_index] != NULL)

				{

					//printf("첫번째%d\n", data_index[first_index]);

					first_index++;

				}



				int second_index = 0; //데이터 인덱스 확인

				while (data_index_second[second_index] != NULL)

				{

					//printf("두번째%d\n", data_index_second[second_index]);

					second_index++;

				}

				int total_num = 0;

				for (int i = 0; i < first_index; i++)

				{

					for (int j = 0; j < second_index; j++)

					{

						if (data_index[i] == data_index_second[j])

						{

							//printf("1같은값%d\n", data_index[i]);

							data_index_total[total_num++] = data_index[i];

							//total_count++;

							break;

						}

						else if (data_index[i] != data_index_second[j])

						{

							//printf("2같은값%d\n", data_index[i]);

							data_index_total[total_num++] = data_index[i];

							//total_count++;

							break;

						}

					}

				}

				int double_num = 0;

				for (int i = 0; i < second_index; i++)

				{

					for (int j = 0; j < first_index; j++) {



						if (data_index_second[i] == data_index[j]) {

							double_num = 0;

							break;

						}

						else if (data_index_second[i] != data_index[j]) {

							double_num = 1;



						}

					}

					if (double_num == 1) {

						data_index_total[total_num++] = data_index_second[i];

						//printf("3같은값%d\n", data_index_second[i]);



						double_num = 0;

					}



				}

				int temp = 0;

				for (int i = 0; i < total_num; i++)

				{

					for (int j = 0; j < (total_num - 1) - i; j++)

					{

						if (data_index_total[j] > data_index_total[j + 1])

						{	// 버블 정렬 사용

							temp = data_index_total[j];

							data_index_total[j] = data_index_total[j + 1];

							data_index_total[j + 1] = temp;

						}

					}

				}



			}

			else if (strcmp(next_col_where, "!=") == 0)

			{



				next_col_where = strtok(NULL, " ");

				//printf("컬럼3 :%s\n", next_col_where); //WHERE 이후 데이터 값



				DATA* where_after_data = where_after_second_col->data;

				int where_after_data_count = 1;



				int data_index_second[100] = { 0, };

				int data_index_count = 0;



				while (where_after_data != NULL)

				{

					if ((strcmp(where_after_data->da_value, next_col_where) == 1) || (strcmp(where_after_data->da_value, next_col_where) == -1))

					{

						data_index_second[data_index_count++] = where_after_data_count;

					}

					where_after_data_count++;

					where_after_data = where_after_data->da_next;

				}



				int first_index = 0; //데이터 인덱스 확인

				while (data_index[first_index] != NULL)

				{

					//printf("첫번째%d\n", data_index[first_index]);

					first_index++;

				}



				int second_index = 0; //데이터 인덱스 확인

				while (data_index_second[second_index] != NULL)

				{

					//printf("두번째%d\n", data_index_second[second_index]);

					second_index++;

				}

				//int total_count = 0;

				int total_num = 0;

				for (int i = 0; i < first_index; i++)

				{

					for (int j = 0; j < second_index; j++) {



						if (data_index[i] == data_index_second[j]) {

							//printf("1같은값%d\n", data_index[i]);

							data_index_total[total_num++] = data_index[i];

							//total_count++;

							break;

						}

						else if (data_index[i] != data_index_second[j]) {

							//printf("2같은값%d\n", data_index[i]);

							data_index_total[total_num++] = data_index[i];

							//total_count++;

							break;

						}

					}

				}

				int double_num = 0;

				for (int i = 0; i < second_index; i++)

				{

					for (int j = 0; j < first_index; j++) {



						if (data_index_second[i] == data_index[j]) {

							double_num = 0;

							break;

						}

						else if (data_index_second[i] != data_index[j]) {

							double_num = 1;



						}

					}

					if (double_num == 1)

					{

						data_index_total[total_num++] = data_index_second[i];

						//printf("3같은값%d\n", data_index_second[i]);



						double_num = 0;

					}



				}

				int temp = 0;

				for (int i = 0; i < total_num; i++)

				{

					for (int j = 0; j < (total_num - 1) - i; j++)

					{

						if (data_index_total[j] > data_index_total[j + 1])

						{	// 버블 정렬 사용

							temp = data_index_total[j];

							data_index_total[j] = data_index_total[j + 1];

							data_index_total[j + 1] = temp;

						}

					}

				}

			}

			COLUMN* cur_col_first = col_cur; //첫번째 col_char를 가르키게 만듬

			int good_index = 0;



			while (data_index_total[good_index] != NULL)

			{

				DATA* cur_data = cur_col_first->data;



				for (int x = 1; x < data_index_total[good_index]; x++)

				{

					cur_data = cur_data->da_next;

				}

				good_index++;

				printf("%s-> ", cur_data->da_value);

				memset(cur_data->da_value, 0, sizeof(char) * 20);

				strcpy(cur_data->da_value, next);

				printf("%s\n", cur_data->da_value);

			}

		}

	}

	printf("*************************\n");

}

void delete_tb_all(TABLE *cur_tb) {

	COLUMN* cur_col = cur_tb->column;

	while (cur_col != NULL)

	{



		DATA* cur_data = cur_col->data;



		while (cur_col->data != NULL)

		{

			cur_col->data = cur_data->da_next;

			

			free(cur_data);



			cur_data = cur_col->data;

		}



		cur_col = cur_col->col_next;

	}

}

void delete_db_all(DB* cur_db) {



	TABLE* cur_tb = cur_db->table;

	while (cur_tb !=NULL)

	{

		COLUMN* cur_col = cur_tb->column;

		while (cur_col != NULL)

		{



			DATA* cur_data = cur_col->data;



			while (cur_col->data != NULL)

			{

				cur_col->data = cur_data->da_next;

				//printf("%s", cur_data->da_value);

				free(cur_data);



				cur_data = cur_col->data;

			}



			cur_col = cur_col->col_next;

		}

		cur_tb = cur_tb->t_next;



	}

	

	

}

//  drop 4.파일 입출력 

////////////////////////////////////////////
#pragma once



#include <stdio.h>

#include <stdlib.h>

#include <string.h>

#include <ctype.h>

#define _CRT_SECURE_NO_WARNINGS



char user_id[100] = { '\0', };

char user_pw[100] = { '\0', };





void log_in(char* name, char* pwd);

void parse_str(char* text);

void create_db(char* new_db);

void show_db();

void show_tb();

void drop_db(char* dbname);

void drop_tb(char* tbname);

void use_db(char* db_name);

void create_tb(char* new_tb);

int create_col(char* new_char);

void insert_data(char* new_data);

void desc_tb(char* new_tb);

void select_data(char* new_tb);

void delete_table(char* newtable);

void update_data(char* newdata);



typedef struct USER {

	struct USER* u_next;

	struct DB* db;

	char user_name[20];

	char user_pwd[20];

}USER;



USER* user_cur;



typedef struct DB {

	struct DB* d_next;

	struct TABLE* table;

	char db_name[20];

}DB;



DB* db_cur;



typedef struct TABLE {

	struct TABLE* t_next;

	struct COLUMN* column;

	char tb_name[20];

}TABLE;



TABLE* tb_cur;



typedef struct COLUMN {

	struct COLUMN* col_next;

	struct DATA* data;

	char col_size[20];

	char col_type[20];

	char col_char[20];

}COLUMN;

COLUMN* col_cur;



typedef struct DATA {

	struct DATA* da_next;

	char da_value[20];

}DATA;



void delete_tb_all(TABLE* cur_tb);

void delete_db_all(DB* cur_db);





