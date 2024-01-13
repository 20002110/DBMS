
## Example Queries

*Note: Query work on MariaDB MariaDB Server Version: 15.1 Distribution: 10.6.12-MariaDB. The query may not work on lower versions.

1. Get name, director, time, premiere of movies that have typeName is 'hành động' .

```bash

    SELECT Name, director, time, premiere FROM Movies WHERE typeID = (SELECT typeID FROM TypeMovie WHERE typeName = 'hành động');

```

                ### result:
                    +----------------------------------+-----------------+-----------+------------+
                    | Name                             | director        | time      | premiere   |
                    +----------------------------------+-----------------+-----------+------------+
                    | PHI CÔNG SIÊU ĐẲNG MAVERICK      | Joseph Kosinski | 130 phút  | 2023-12-23 |
                    +----------------------------------+-----------------+-----------+------------+


2. Get name movie, name theater, date, time of Books of user have user_id is 1.

```bash 

    SELECT Movies.Name, theater.theaterName, Rooms.date, Rooms.time FROM Movies, theater, Rooms, Books WHERE Books.user_Id = 1 AND Books.seatID = Rooms.seatID AND Rooms.MovieID = Movies.movieID AND Rooms.theaterID = theater.theaterID;

```

                ### result:
                    +-------------------------------+-------------------------+------------+----------+
                    | Name                          | theaterName             | date       | time     |
                    +-------------------------------+-------------------------+------------+----------+
                    | THIẾU NIÊN VÀ CHIM DIỆC       | CGV Hùng Vương Plaza    | 2023-12-30 | 08:00:00 |
                    | KẺ ĂN HỒN                     | CGV Hùng Vương Plaza    | 2023-12-24 | 10:00:00 |
                    | WONKA                         | CGV Vĩnh Trung Plaza    | 2023-12-30 | 08:00:00 |
                    +-------------------------------+-------------------------+------------+----------+


3. Get name, phone, date_of_birth, address of user books ticket of movie have movieName is 'KẺ ĂN HỒN'.

``` bash

    SELECT Users.fullName, Users.phone, Users.date_of_birth, Users.address FROM Users, Movies, Rooms, Books WHERE Books.seatID = Rooms.seatID AND Rooms.MovieID = Movies.movieID AND Books.user_Id = Users.user_id AND Movies.Name = 'KẺ ĂN HỒN';

```

                ### result:
                    +-------------------------+------------+---------------+------------------------------------+
                    | fullName                | phone      | date_of_birth | address                            |
                    +-------------------------+------------+---------------+------------------------------------+
                    | NGUYỄN TIẾN ĐẠT         | 0122993242 | 2002-01-17    | ngách 224/80 đường hoàng mai       |
                    | Trung Lưu               | 0392134311 | 2002-11-27    | Thái Nguyên                        |
                    +-------------------------+------------+---------------+------------------------------------+



4. Get name, username, phone, cost, theaterName, seatName of user books ticket of movie have movieName is 'WONKA'. (Note: user_id of seatMap is user_id of user books ticket and ticketID of seatMap is ticketID of user books ticket).

``` bash

    SELECT
        U.fullName,
        U.username,
        U.phone,
        M.cost,
        T.theaterName,
        seatInfo.seatName
    FROM
        Users U
    JOIN
        Books B ON U.user_id = B.user_Id
    JOIN
        Rooms R ON B.seatID = R.seatID
    JOIN
        Movies M ON R.MovieID = M.movieID
    JOIN
        theater T ON R.theaterID = T.theaterID
    JOIN
        JSON_TABLE(
            R.seatMap,
            '$[*]' COLUMNS (
                user_id INT PATH '$.user_id',
                status INT PATH '$.status',
                ticketID INT PATH '$.ticketID',
                seatName VARCHAR(255) PATH '$.seatName'
            )
        ) AS seatInfo
    WHERE
        M.Name = 'WONKA'
        AND seatInfo.user_id = B.user_id
        AND seatInfo.status = 1
        AND seatInfo.ticketID = B.ticketID;

```



                    ### result: 
                        +-------------------------+----------------------+------------+-------+-----------------------+----------+
                        | fullName                | username             | phone      | cost  | theaterName           | seatName |
                        +-------------------------+----------------------+------------+-------+-----------------------+----------+
                        | NGUYỄN TIẾN ĐẠT         | danhnt1211@gmail.com | 0122993242 | 65000 | CGV Vĩnh Trung Plaza  | A1       |
                        | NGUYỄN TIẾN ĐẠT         | danhnt1211@gmail.com | 0122993242 | 65000 | CGV Vĩnh Trung Plaza  | C1       |
                        +-------------------------+----------------------+------------+-------+-----------------------+----------+





5. Get name, username, phone, cost, theaterName, movieName, seatName of user books ticket of user have user_id = 1. (Note: user_id of seatMap is user_id of user books ticket and ticketID of seatMap is ticketID of user books ticket).

``` bash

    SELECT
        U.fullName,
        U.username,
        U.phone,
        M.cost,
        T.theaterName,
        M.Name,
        seatInfo.seatName
    FROM
        Users U
    JOIN
        Books B ON U.user_id = B.user_Id
    JOIN
        Rooms R ON B.seatID = R.seatID
    JOIN
        Movies M ON R.MovieID = M.movieID
    JOIN
        theater T ON R.theaterID = T.theaterID
    JOIN
        JSON_TABLE(
            R.seatMap,
            '$[*]' COLUMNS (
                user_id INT PATH '$.user_id',
                status INT PATH '$.status',
                ticketID INT PATH '$.ticketID',
                seatName VARCHAR(255) PATH '$.seatName'
            )
        ) AS seatInfo
    WHERE
        U.user_id = 1
        AND seatInfo.user_id = B.user_id
        AND seatInfo.status = 1
        AND seatInfo.ticketID = B.ticketID;

```


                ### result:
                    +-------------------------+----------------------+------------+-------+-------------------------+-------------------------------+----------+
                    | fullName                | username             | phone      | cost  | theaterName             | Name                          | seatName |
                    +-------------------------+----------------------+------------+-------+-------------------------+-------------------------------+----------+
                    | NGUYỄN TIẾN ĐẠT         | danhnt1211@gmail.com | 0122993242 | 50000 | CGV Hùng Vương Plaza    | THIẾU NIÊN VÀ CHIM DIỆC       | A2       |
                    | NGUYỄN TIẾN ĐẠT         | danhnt1211@gmail.com | 0122993242 | 50000 | CGV Hùng Vương Plaza    | THIẾU NIÊN VÀ CHIM DIỆC       | B2       |
                    | NGUYỄN TIẾN ĐẠT         | danhnt1211@gmail.com | 0122993242 | 65000 | CGV Hùng Vương Plaza    | KẺ ĂN HỒN                     | A2       |
                    | NGUYỄN TIẾN ĐẠT         | danhnt1211@gmail.com | 0122993242 | 65000 | CGV Hùng Vương Plaza    | KẺ ĂN HỒN                     | C1       |
                    | NGUYỄN TIẾN ĐẠT         | danhnt1211@gmail.com | 0122993242 | 65000 | CGV Vĩnh Trung Plaza    | WONKA                         | A1       |
                    | NGUYỄN TIẾN ĐẠT         | danhnt1211@gmail.com | 0122993242 | 65000 | CGV Vĩnh Trung Plaza    | WONKA                         | C1       |
                    +-------------------------+----------------------+------------+-------+-------------------------+-------------------------------+----------+

6. Simulation have user books ticket of movie have movieName is 'THIẾU NIÊN VÀ CHIM DIỆC' and userName is 'Vi Anh Quân' at 08:00:00 22/12/2023 in theater have theaterName is 'CGV Vincom Center Bà Triệu' and seatName is A1.


```bash

SET @fullName = 'Vi Anh Quân';
SET @movieName = 'THIẾU NIÊN VÀ CHIM DIỆC';
SET @theaterName = 'CGV Vincom Center Bà Triệu';
SET @seatName = 'A1';
SET @time = '08:00:00';
SET @date = '2023-12-22';

SET @user_id = (SELECT user_id FROM Users WHERE fullName = @fullName);
SET @movieID = (SELECT movieID FROM Movies WHERE Name = @movieName);
SET @theaterID = (SELECT theaterID FROM theater WHERE theaterName = @theaterName);
SET @seatID = (SELECT seatID FROM Rooms WHERE MovieID = @movieID AND theaterID = @theaterID AND time = @time AND date = @date);
SET @seatMap = (SELECT seatMap FROM Rooms WHERE MovieID = @movieID AND theaterID = @theaterID AND time = @time AND date = @date);

SET @status = (SELECT seatInfo.status FROM JSON_TABLE(@seatMap, '$[*]' COLUMNS (user_id INT PATH '$.user_id', status INT PATH '$.status', ticketID INT PATH '$.ticketID', seatName VARCHAR(255) PATH '$.seatName')) AS seatInfo WHERE seatInfo.seatName = @seatName);

DELIMITER $$
IF @status = 1 THEN

    SELECT 'Seat is booked';

ELSE 

    INSERT INTO Books (user_Id, seatID) VALUES (@user_id, @seatID);

    SET @ticketID = (SELECT MAX(ticketID) FROM Books );

    SET @jsonPathseatName = JSON_UNQUOTE(JSON_SEARCH(@seatMap, 'one', @seatName));
    SET @start = LOCATE('[', @jsonPathseatName) - 1;
    SET @end = LOCATE(']', @jsonPathseatName) + 1;
    SET @jsonPath = SUBSTRING(@jsonPathseatName, @start, @end - @start);

    SET @jsonPathstatus = CONCAT(@jsonPath, '.status');
    SET @jsonPathticketID = CONCAT(@jsonPath, '.ticketID');
    SET @jsonPathuserID = CONCAT(@jsonPath, '.user_id');

    SET @seatMap = JSON_REPLACE(@seatMap, @jsonPathstatus, 1);
    SET @seatMap = JSON_REPLACE(@seatMap, @jsonPathticketID,  @ticketID );
    SET @seatMap = JSON_REPLACE(@seatMap, @jsonPathuserID, @user_id);

    UPDATE Rooms SET seatMap = @seatMap WHERE seatID = @seatID;



    SELECT 'Success';
END IF $$
DELIMITER ;

```
                ### result:
                    +---------+
                    | Success |
                    +---------+
                    | Success |
                    +---------+
