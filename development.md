java List转成Tree
List<KnowledgeTree> newTreeList = knowledgeTreeList.stream().filter(tree -> tree.getParentId()==null).peek(menu -> {
            menu.setChildren(getChildren(menu, knowledgeTreeList));
        }).collect(Collectors.toList());
   
private List<KnowledgeTree> getChildren(KnowledgeTree root, List<KnowledgeTree> all) {
        return all.stream().filter(m1 -> root.getId().equals(m1.getParentId()))
                .peek(m1 -> m1.setChildren(getChildren(m1, all)))
                .collect(Collectors.toList());
    }   
