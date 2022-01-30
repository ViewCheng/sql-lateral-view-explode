在ETL过程中对row data进行处理，我们会经常用到lateral view explode。
1: 什么情况下需要使用？

    students: {
        type: "array"
        student: {
            type: "struct"
            name: "xiaoming"
            notebooks: {
                type: "array"
                notebook: {
                    type: "string"
                    name: "English"
                }
                notebook: {
                    type: "string"
                    name: "Law"
                }
                ....
            }
            .....
        }
        student:{
            type: "struct"
            name: "xiaohong"
            .....
        }
    }
    
当我们需要处理的对象是一个array的情况下，对于他的下一级会有多个student, 我们是无法通过data.students.student.name去选中所有的student的名字的，因为他将会是多个，系统是无法理解的。

2: 如何使用？
select students.student.name as studentName
later view explode(data.students) s as students
from data

3:逻辑，later view explode 将会创建一个虚拟表去展开每一个student成为新的一列（row）。然后在单独对一行在进行处理。

4: 拓展
later view outer explode

select 
students.student.name as studentName
otebooks.notebook.name as notebookName
later view explode(data.students) s as students
later view explode(students.notebooks) n as notebooks
from data
当我们如下所示进行explode，对于一个格式，我们可以拿到所有的数据，但是对于第二个格式，我们将会连第一个student的名字都会失去。
因为我们进行两次explode并置于前者的后面，当第二个为空的时候，第一个也会失去，会导致我们拿到的数据并不可靠，基于这个情况，我们就需要使用到outer explode。
