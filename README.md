# symfonyHack
Here is a dirty-hack to enable doctrine:generate:entities with psr-4.
Here is a dirty-hack to enable doctrine:generate:entities with psr-4. or you can download those 3 files to your Symfony

In Mapping/DisconnectedMetadataFactory.php, line 144 :
```
    private function getBasePathForClass($name, $namespace, $path) {
        $reflector = new \ReflectionClass($name);
        $destination = dirname($reflector->getFileName());
        return $destination;

/*
        $namespace = str_replace('\\', '/', $namespace);
        $search = str_replace('\\', '/', $path);
        $destination = str_replace('/' . $namespace, '', $search, $c);
        if ($c != 1) {
            throw new \RuntimeException(sprintf('Can\'t find base path for "%s" (path: "%s", destination: "%s").', $name, $path, $destination));
        }

        return $destination;
*/
    }
```
In Doctrine\ORM\Tools\EntityGenerator.php
```
    public function writeEntityClass(ClassMetadataInfo $metadata, $outputDirectory)
    {
        // hack fazae for psr-4
        $names= explode("\\", $metadata->name);
        $name=  array_pop($names);
        $path = $outputDirectory . '/' . $name . $this->extension;
//        $path = $outputDirectory . '/' . str_replace('\\', DIRECTORY_SEPARATOR, $metadata->name) . $this->extension;
        // end of hack
        $dir = dirname($path);
       ...
```
In Doctrine\ORM\Tools\EntityRepositoryGenerator.php
```
    public function writeEntityRepositoryClass($fullClassName, $outputDirectory)
    {
        $code = $this->generateEntityRepositoryClass($fullClassName);

        // hack fazae for psr-4
        $names= explode("\\", $fullClassName);
        $name=  array_pop($names);
        $path = $outputDirectory . DIRECTORY_SEPARATOR  . str_replace('\\', \DIRECTORY_SEPARATOR, $name) . '.php';
//        $path = $outputDirectory . DIRECTORY_SEPARATOR
//              . str_replace('\\', \DIRECTORY_SEPARATOR, $fullClassName) . '.php';
        // end of hack
```
It works with psr-4 and you can do "app/console doctrine:generate:entities myBundle:myEntity"
Warning: entities are in folder: mybundle/Entity/myEntity.php
