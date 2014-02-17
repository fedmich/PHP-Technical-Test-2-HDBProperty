PHP-Technical-Test-2-HDBProperty
================================

Test answers for PropertyGuru



Assumptions:
1. The webserver has been configured correctly to direct the URL specify below to getinfo.php.
2. The Mysql Server has been configured and running.

Please raise at least 10 suggestions/corrections about these codes.

test.html:

    <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <HTML xmlns:fb="http://www.facebook.com/2008/fbml">
    <HEAD>
    <SCRIPT SRC="http://code.jquery.com/jquery-1.7.2.js"></SCRIPT>
    <script>
    <!--//
    Get the data using ajax
    //-->
    $.get(
        "/getinfo/111", {},
        function(data) { $('.InfoDiv').html(data); }
    );
    </script>
    </HEAD>
    <BODY>
    <H1><FONT FACE="verdana" COLOR="green">Welcome</FONT></H1>
    <CENTER><DIV CLASS="InfoDiv"></DIV></CENTER>
    </BODY>
    <DIV ID="fb-root"></DIV>
    <SCRIPT></SCRIPT>
    </HTML>

getinfo.php:

    <?php
    require_once("data.php");

    $ArrayURL = split('/', $_SERVER[REQUEST_URI]));
    $id = $ArrayURL[1];
    $data = new dataObj();

    if (is_object($data) = true) $status = '200 OK';
    $status_header = 'HTTP/1.1 $status';

    header($status_header);
    return json_encode( $data->getAll($id) );

    ?>

data.php:

    <?php

    class baseObj {
        public $mysql = null;
        private $table = null;

        public function __construct ()
        {
            $this->mysql = new mysqli("localhost", "user", "password", "database");
            if ($this->mysql->connect_errno) {
                echo "Failed to connect to MySQL: (" . $this->mysql->connect_errno . ") " . $this->mysql->connect_error;
            }
        }

        public function get ($id, $field)
        {
            return $this->mysql->query("SELECT $field FROM $table WHERE ID = $id");
        }

        public function getAll ($id)
        {
            $res = $this->mysql->query("SELECT * FROM $table WHERE ID = $id");
            return $res->fetch_assoc();
        }
    }

    class propertyData extends baseObj {
        public $id = null;
        public $type = null;
        public $title = null;
        public $address = null;
        public $bedroom = null;
        public $livingroom = null;
        public $diningroom = null;
        protected $hdbblock = null;
        private $swimmingPool = null;

        private $table = 'Property';

        public function getType ($ID) { $Type = $this->get( $ID, 'Type'); return $Type; }
        public function getTitle ($ID) { $Title = $this->get( $ID, 'Title') ; return $Type;}
        public function getAddress ($ID) { $Address = $this->get( $ID, 'Address') ; return $Address;}
        public function getBedroom ($ID) { $Bedroom = $this->get( $ID, 'Bedroom') ; return $Bedroom;}
        public function getLivingroom ($ID) { $livingroom = $this->get( $ID, 'Living_room') ; return $livingroom;}
        public function getDiningroom ($ID) { $diningroom = $this->get( $ID, 'Diningroom') ; return $diningroom;}
    }

    class hdbData extends propertyData {
        private $table = 'HDB';
        public function getHDBBlock ($ID) { $this->hdbblock = $this->get($ID, 'HDBBlock'); return $this->hdbblock; }
    }

    class condoData extends propertyData {
        private $table = 'ConDO';
        public function gotSwimmingPool ($ID)
        {
            return $this->get($ID, 'SwimmingPool');
        }
    }

    ?>

data.sql:

    CREATE TABLE Property (
        ID BIGINT AUTO_INCREMENT PRIMARY KEY,
        Type TINYINT default 1,
        Title CHAR(255) default '',
        Address TEXT NOT NULL,
        Bedroom INT default 0,
        Living_room INT default 0,
        Diningroom INT default 0,
        Size Decimal(6,2) default 0.0
    ) ENGINE=MyISAM;

    CREATE TABLE HDB (
        ID BIGINT AUTO_INCREMENT PRIMARY KEY,
        PID BIGINT NOT NULL,
        HDBBlock INT NOT NULL,
        INDEX idx_PID (PID), 
        FOREIGN KEY (PID) REFERENCES Property(ID)
    ) ENGINE=InnoDB;

    CREATE TABLE Condo (
        ID BIGINT AUTO_INCREMENT,
        PID INT NOT NULL,
        SwimmingPool TINYINT default 0,
        INDEX idx_PID (PID), 
        FOREIGN KEY (PID) REFERENCES Property(ID)
    ) ENGINE=InnoDB;

