<img title="propel-logo" src="/blog/medias/2010-05-13-propel-utiliser-des-champs-calcules/propel-logo-e1273745027780-150x40.png" alt="propel logo e1273745027780 150x40 Propel   Utiliser des champs calculés" width="150" height="40" />
Il est parfois très utile de pouvoir récupérer directement des champs calculés depuis la base de données, soit parce que passer par un criteria s'avère très compliqué, voir impossible, soit pour confier les calculs à la base de données.

Je vous propose donc aujourd'hui deux solutions pour le faire avec Propel :

### 1ère solution, les résultats sont accédés via des index numériques

```
//QUERY
$query = SELECT DISTINCT('.UserPeer::ID.'), '.UserPeer::FIRSTNAME.', '.UserPeer::LASTNAME.',
                         '.UserPeer::NICKNAME.', COUNT('.QuestionPeer::ID.') AS NB_QUESTIONS,
                         '.QuestionPeer::LEVEL.'
         FROM '.UserPeer::TABLE_NAME.'
         INNER JOIN '.QuestionPeer::TABLE_NAME.'
         ON '.QuestionPeer::USER_ID.' = '.UserPeer::ID.'
         WHERE '.QuestionPeer::IS_VALID.' = 1'
         GROUP BY '.UserPeer::ID.', '.QuestionPeer::LEVEL.'
         ORDER BY '.QuestionPeer::LEVEL;
$stmt = $con->prepareStatement($query);
$rs = $stmt->executeQuery(ResultSet::FETCHMODE_NUM);

// PARSING RESULTS
while ($rs->next())
{
  $users[$rs->getInt(1)] = array('nickname'  => $rs->getString(4),
                                 'firstname' => $rs->getString(2),
                                 'lastname'  => $rs->getString(3),
                           );

  $level = $rs->getInt(6);
  $questions[$rs->getInt(1)][(($level < 5) ? $level : 5)] = $rs->getInt(5);
  $users[$rs->getInt(1)]['questions']                     = $questions[$rs->getInt(1)];
}
```

### 2ème solution, les résultats sont accédés via les noms de champs renvoyés par la requête

Il suffit pour cela de remplacer la méthode de récupération de executeQuery en utilisant cette fois:

```
ResultSet::FETCHMODE_ASSOC
```

```
$rs = $stmt->executeQuery(ResultSet::FETCHMODE_ASSOC);
// PARSING RESULTS
while ($rs->next())
{
  $record = $rs->getRow();
  $users[$record['ID'] = array('nickname'  => $record['NICKNAME'],
                               'firstname' => $record['FIRSTNAME'],
                               'lastname'  => $record['LASTNAME'],
                           );

  $level = $records['LEVEL'];
  $questions[$record['ID']][(($level < 5) ? $level : 5)] = $record['NB_QUESTIONS'];
  $users[record['ID']]['questions']                      = $questions[$record['ID']];
}
```

Pour ceux qui souhaitent mixer les deux méthodes, il est tout à fait possible de demander à un Criteria, de renvoyer un tableau indexé à partir
des noms de champs, ou des index numériques d'ailleurs.
Nous allons de la même façon que ci-dessus spécifier à Propel, la façon dont nous souhaitons récupérer nos résultats, pour le coup nous passerons
par la méthode

```
doSelectRS()
```

afin de récupérer des resultSet et non des objets.

```
$c = new Criteria();

$c->clearSelectColumns();
$c->addSelectColumn(UserPeer::NICKNAME);
$c->addSelectColumn(QuestionPeer::ID);
$c->addSelectColumn(QuestionPeer::BODY);
$c->addSelectColumn(QuestionPeer::TITLE);
$c->addSelectColumn(QuestionPeer::CREATED_AT);
$c->addSelectColumn(QuestionPeer::LEVEL);
$c->addSelectColumn(QuestionPeer::INTERESTED_USERS);
$c->addSelectColumn(QuestionPeer::NB_ANSWERS);

$c->add(QuestionPeer::IS_VALID, true);

if(!empty($filterDateFrom) &amp;&amp; !empty($filterDateTo))
{
  $c->add(QuestionPeer::CREATED_AT, QuestionPeer::CREATED_AT." BETWEEN '".$filterDateFrom."'AND '".$filterDateTo."'", Criteria::CUSTOM);
}

$rs = UserPeer::doSelectRs($c);
$rs->setFetchMode(ResultSet::FETCHMODE_ASSOC);

// PARSING
while ($rs->next())
{
  $records = $rs->getRow();
  $results[$records['ID']] =
    array('nickname'            => $records['NICKNAME'],
          'body'                => $records['BODY'],
          'title'               => $records['TITLE'],
          'created_at'          => $records['CREATED_AT'],
          'level'               => $records['LEVEL'],
          'interested_users'    => $records['INTERESTED_USERS'],
          'nb_answers'          => $records['NB_ANSWERS'],
          );
}
```