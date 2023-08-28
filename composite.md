# 组合模式

组合模式是一种**结构型**模式。

组合模式中，最常用的一个用法就是目录层级的遍历：

```cpp
// 抽象节点类
class Node
{
 protected:
  string m_content;
  virtual void print();
};

// 叶子节点类
class LeafNode : public Node
{
 public:
  LeafNode(string content)
  {
    m_content = content;
  }

  void print()
  {
    cout << m_content << endl;
  }
};

// 树枝节点类
class BranchNode : public Node
{
 private:
  // BranchNode 下包含多个 Node
  std::list<Node*> nodes;
 public:
  std::list<Node*> getNodes()
  {
    return nodes;
  }
  BranchNode(string content)
  {
    m_content = content;
  }
  void print() override
  {
    cout << m_content << endl;
  }
  void addNode(Node* node)
  {
    nodes.push_back(node);
  }
};

void tree(Node* node, int depth)
{
  for(int i = 0; i < depth; i++)
  {
    cout << "--";
  }
  node->print();
  BranchNode* branch = dynamic_cast<BranchNode*>(node);
  if(branch)
  {
    std::list<Node*> nodes = branch->getNodes();
    for(Node* x : nodes)
    {
      tree(x, depth + 1);
    }
  }
}

int main()
{
  BranchNode* root = new BranchNode("root");
  BranchNode* branch1 = new BranchNode("branch1");
  BranchNode* branch2 = new BranchNode("branch2");
  branch1->addNode(new LeafNode("leaf1"));
  root->addNode(branch1);
  root->addNode(branch2);
  tree(root, 0);
  return 0;
}
```
